# IndexStreamTTS Usage Guide

## Environment Setup
### 1. Clone Project 
```bash 
git clone https://github.com/Ksuriuri/index-tts-vllm.git
```
Enter the extracted directory
```bash
cd index-tts-vllm
```
Switch to specific version (using historical version of VLLM-0.10.2)
```bash
git checkout 224e8d5e5c8f66801845c66b30fa765328fd0be3
```

### 2. Create and Activate conda Environment
```bash 
conda create -n index-tts-vllm python=3.12
conda activate index-tts-vllm
```

### 3. Install PyTorch (Required version 2.8.0 - Latest)
#### Check GPU Maximum Supported Version and Actual Installed Version
```bash
nvidia-smi
nvcc --version
``` 
#### Maximum CUDA Version Supported by Driver
```bash
CUDA Version: 12.8
```
#### Actual Installed CUDA Compiler Version
```bash
Cuda compilation tools, release 12.8, V12.8.89
```
#### Corresponding Installation Command (PyTorch defaults to 12.8 driver version)
```bash
pip install torch torchvision
```
Requires PyTorch version 2.8.0 (corresponding to vllm 0.10.2). For specific installation instructions, refer to: [PyTorch Official Website](https://pytorch.org/get-started/locally/)

### 4. Install Dependencies
```bash 
pip install -r requirements.txt
```

### 5. Download Model Weights
### Option 1: Download Official Weight Files and Convert
These are official weight files that can be downloaded to any local path, supporting IndexTTS-1.5 weights  
| HuggingFace                                                   | ModelScope                                                          |
|---------------------------------------------------------------|---------------------------------------------------------------------|
| [IndexTTS](https://huggingface.co/IndexTeam/Index-TTS)        | [IndexTTS](https://modelscope.cn/models/IndexTeam/Index-TTS)        |
| [IndexTTS-1.5](https://huggingface.co/IndexTeam/IndexTTS-1.5) | [IndexTTS-1.5](https://modelscope.cn/models/IndexTeam/IndexTTS-1.5) |

Using ModelScope installation method as example  
#### Note: Git needs to be installed and initialized with lfs enabled (skip if already installed)
```bash
sudo apt-get install git-lfs
git lfs install
```
Create model directory and pull model
```bash 
mkdir model_dir
cd model_dir
git clone https://www.modelscope.cn/IndexTeam/IndexTTS-1.5.git
```

#### Model Weight Conversion
```bash 
bash convert_hf_format.sh /path/to/your/model_dir
```
For example: If your downloaded IndexTTS-1.5 model is stored in the model_dir directory, execute the following command
```bash
bash convert_hf_format.sh model_dir/IndexTTS-1.5
```
This operation will convert official model weights to a transformers library-compatible version, saved in the vllm folder under the model weight path, making it convenient for subsequent vllm library model weight loading

### 6. Modify Interface to Adapt the Project
The interface return data is not compatible with the project and needs adjustment to directly return audio data
```bash
vi api_server.py
```
```bash 
@app.post("/tts", responses={
    200: {"content": {"application/octet-stream": {}}},
    500: {"content": {"application/json": {}}}
})
async def tts_api(request: Request):
    try:
        data = await request.json()
        text = data["text"]
        character = data["character"]

        global tts
        sr, wav = await tts.infer_with_ref_audio_embed(character, text)

        return Response(content=wav.tobytes(), media_type="application/octet-stream")
        
    except Exception as ex:
        tb_str = ''.join(traceback.format_exception(type(ex), ex, ex.__traceback__))
        print(tb_str)
        return JSONResponse(
            status_code=500,
            content={
                "status": "error",
                "error": str(tb_str)
            }
        )
```

### 7. Write sh Startup Script (Note: Must run in corresponding conda environment)
```bash 
vi start_api.sh
```
### Paste the content below and press: input wq to save  
#### Please modify /home/system/index-tts-vllm/model_dir/IndexTTS-1.5 in the script to the actual path
```bash
# Activate conda environment
conda activate index-tts-vllm 
echo "Activating project conda environment"
sleep 2
# Find process ID using port 11996
PID_VLLM=$(sudo netstat -tulnp | grep 11996 | awk '{print $7}' | cut -d'/' -f1)

# Check if process ID is found
if [ -z "$PID_VLLM" ]; then
  echo "No process found using port 11996"
else
  echo "Process using port 11996 found, process ID: $PID_VLLM"
  # Try normal kill first, wait 2 seconds
  kill $PID_VLLM
  sleep 2
  # Check if process still exists
  if ps -p $PID_VLLM > /dev/null; then
    echo "Process still running, force terminate..."
    kill -9 $PID_VLLM
  fi
  echo "Terminated process $PID_VLLM"
fi

# Find VLLM::EngineCore processes
GPU_PIDS=$(ps aux | grep -E "VLLM|EngineCore" | grep -v grep | awk '{print $2}')

# Check if process ID is found
if [ -z "$GPU_PIDS" ]; then
  echo "No VLLM related processes found"
else
  echo "VLLM related processes found, process IDs: $GPU_PIDS"
  # Try normal kill first, wait 2 seconds
  kill $GPU_PIDS
  sleep 2
  # Check if processes still exist
  if ps -p $GPU_PIDS > /dev/null; then
    echo "Processes still running, force terminate..."
    kill -9 $GPU_PIDS
  fi
  echo "Terminated processes $GPU_PIDS"
fi

# Create tmp directory (if it doesn't exist)
mkdir -p tmp

# Run api_server.py in background, redirect logs to tmp/server.log
nohup python api_server.py --model_dir /home/system/index-tts-vllm/model_dir/IndexTTS-1.5 --port 11996 > tmp/server.log 2>&1 &
echo "api_server.py is running in background, check logs at tmp/server.log"
```
Give script execution permissions and run the script
```bash 
chmod +x start_api.sh
./start_api.sh
```
Logs will be output in tmp/server.log, you can view log status with the following command
```bash
tail -f tmp/server.log
```
If GPU memory is sufficient, you can add the startup parameter ----gpu_memory_utilization in the script to adjust GPU memory usage ratio, default value is 0.25

## Voice Configuration
index-tts-vllm supports registering custom voices through configuration files, supporting both single voice and mixed voice configurations.  
Configure custom voices in the assets/speaker.json file in the project root directory
### Configuration Format Description
```bash
{
    "Speaker Name 1": [
        "Audio File Path 1.wav",
        "Audio File Path 2.wav"
    ],
    "Speaker Name 2": [
        "Audio File Path 3.wav"
    ]
}
```
### Note (Service restart required for voice registration after configuring roles)
After adding, you need to add the corresponding speaker in the Smart Control Panel (for single module, change the corresponding voice)