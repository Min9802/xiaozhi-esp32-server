# Vision Model Usage Guide
This tutorial is divided into two parts:
- Part 1: Running xiaozhi-server in single module mode to enable vision model
- Part 2: How to enable vision model when running in full module mode

Before enabling the vision model, you need to prepare three things:
- You need to prepare a device with a camera, and this device has already implemented camera calling functionality in XiaGe's repository. For example, `Lichuang ESP32-S3 Development Board`
- Your device firmware version needs to be upgraded to 1.6.6 or above
- You have successfully run the basic conversation module

## Running xiaozhi-server in Single Module Mode to Enable Vision Model

### Step 1: Confirm Network
Since the vision model will start on port 8003 by default.

If you are running with Docker, please confirm whether your `docker-compose.yml` has the `8003` port. If not, update to the latest `docker-compose.yml` file.

If you are running from source code, confirm whether the firewall allows port `8003`.

### Step 2: Choose Your Vision Model
Open your `data/.config.yaml` file and set your `selected_module.VLLM` to a vision model. Currently, we support vision models with `openai` type interfaces. `ChatGLMVLLM` is one of the models compatible with `openai`.

```
selected_module:
  VAD: ..
  ASR: ..
  LLM: ..
  VLLM: ChatGLMVLLM
  TTS: ..
  Memory: ..
  Intent: ..
```
selected_module:
  VAD: ..
  ASR: ..
  LLM: ..
  VLLM: ChatGLMVLLM
  TTS: ..
  Memory: ..
  Intent: ..
```

Assuming we use `ChatGLMVLLM` as the vision model, we need to first log in to the [Zhipu AI](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) website and apply for an API key. If you have already applied for a key before, you can reuse that key.

Add this configuration to your configuration file. If you already have this configuration, just set your api_key.

```
VLLM:
  ChatGLMVLLM:
    api_key: your_api_key
```

### Step 3: Start xiaozhi-server Service
If you are running from source code, input the command to start
```
python app.py
```
If you are running with Docker, restart the container
```
docker restart xiaozhi-esp32-server
```

After starting, the following log content will be output.

```
2025-06-01 **** - OTA Interface:        http://192.168.4.7:8003/xiaozhi/ota/
2025-06-01 **** - Vision Analysis Interface: http://192.168.4.7:8003/mcp/vision/explain
2025-06-01 **** - Websocket Address:    ws://192.168.4.7:8000/xiaozhi/v1/
2025-06-01 **** - =======The above addresses are websocket protocol addresses, do not access with browser=======
2025-06-01 **** - To test websocket, please use Chrome browser to open test_page.html in the test directory
2025-06-01 **** - =============================================================
```

After starting, use your browser to open the `Vision Analysis Interface` link in the logs. See what it outputs? If you are on Linux without a browser, you can execute this command:
```
curl -i your_vision_analysis_interface
```

Normally it will display like this
```
MCP Vision interface running normally, vision explanation interface address is: http://xxxx:8003/mcp/vision/explain
```

Please note, if you are deploying on public network or using Docker deployment, you must modify this configuration in your `data/.config.yaml`
```
server:
  vision_explain: http://your_ip_or_domain:port_number/mcp/vision/explain
```

Why? Because the vision explanation interface needs to be sent to the device. If your address is a local network address or Docker internal address, the device cannot access it.

Assuming your public network address is `111.111.111.111`, then `vision_explain` should be configured like this

```
server:
  vision_explain: http://111.111.111.111:8003/mcp/vision/explain
```

If your MCP Vision interface is running normally and you can also access the delivered `vision explanation interface address` normally with your browser, please continue to the next step

### Step 4: Device Wake-up Activation

Say to the device "Please turn on the camera and tell me what you see"

Pay attention to the xiaozhi-server log output and see if there are any errors.


## How to Enable Vision Model When Running in Full Module Mode

### Step 1: Confirm Network
Since the vision model will start on port 8003 by default.

If you are running with Docker, please confirm whether your `docker-compose_all.yml` has mapped the `8003` port. If not, update to the latest `docker-compose_all.yml` file.

If you are running from source code, confirm whether the firewall allows port `8003`.

### Step 2: Confirm Your Configuration File

Open your `data/.config.yaml` file and confirm whether the structure of your configuration file is the same as `data/config_from_api.yaml`. If it's different or missing items, please complete them.

### Step 3: Configure Vision Model API Key

We need to first log in to the [Zhipu AI](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) website and apply for an API key. If you have already applied for a key before, you can reuse that key.

Log in to the `Smart Control Panel`, click `Model Configuration` in the top menu, click `Vision Language Model` in the left sidebar, find `VLLM_ChatGLMVLLM`, click the modify button, in the popup, enter your API key in the `API Key` field, and click save.

After successful saving, go to the agent you need to test, click `Configure Role`, in the opened content, check whether `Vision Large Language Model (VLLM)` has selected the vision model you just configured. Click save.

### Step 4: Start xiaozhi-server Module
If you are running from source code, input the command to start
```
python app.py
```
If you are running with Docker, restart the container
```
docker restart xiaozhi-esp32-server
```

After starting, the following log content will be output.

```
2025-06-01 **** - Vision Analysis Interface: http://192.168.4.7:8003/mcp/vision/explain
2025-06-01 **** - Websocket Address:    ws://192.168.4.7:8000/xiaozhi/v1/
2025-06-01 **** - =======The above addresses are websocket protocol addresses, do not access with browser=======
2025-06-01 **** - To test websocket, please use Chrome browser to open test_page.html in the test directory
2025-06-01 **** - =============================================================
```

After starting, use your browser to open the `Vision Analysis Interface` link in the logs. See what it outputs? If you are on Linux without a browser, you can execute this command:
```
curl -i your_vision_analysis_interface
```

Normally it will display like this
```
MCP Vision interface running normally, vision explanation interface address is: http://xxxx:8003/mcp/vision/explain
```

Please note, if you are deploying on public network or using Docker deployment, you must modify this configuration in your `data/.config.yaml`
```
server:
  vision_explain: http://your_ip_or_domain:port_number/mcp/vision/explain
```

Why? Because the vision explanation interface needs to be sent to the device. If your address is a local network address or Docker internal address, the device cannot access it.

Assuming your public network address is `111.111.111.111`, then `vision_explain` should be configured like this

```
server:
  vision_explain: http://111.111.111.111:8003/mcp/vision/explain
```

If your MCP Vision interface is running normally and you can also access the delivered `vision explanation interface address` normally with your browser, please continue to the next step

### Step 5: Device Wake-up Activation

Say to the device "Please turn on the camera and tell me what you see"

Pay attention to the xiaozhi-server log output and see if there are any errors.
