# Frequently Asked Questions ❓

### 1. Why does Xiaozhi recognize a lot of Korean, Japanese, and English when I speak? 🇰🇷

Suggestion: Check if there is a `model.pt` file in `models/SenseVoiceSmall`. If not, you need to download it. See here: [Download Speech Recognition Model Files](Deployment.md#model-files)

### 2. Why does "TTS task error, file does not exist" appear? 📁

Suggestion: Check if you have correctly installed the `libopus` and `ffmpeg` libraries using `conda`.

If not installed, install them:

```
conda install conda-forge::libopus
conda install conda-forge::ffmpeg
```

### 3. TTS often fails and times out ⏰

Suggestion: If `EdgeTTS` often fails, first check if you are using a proxy (VPN). If so, try turning off the proxy and try again.  
If you're using Volcano Engine's Doubao TTS and it frequently fails, it's recommended to use the paid version, as the test version only supports 2 concurrent connections.

### 4. Can connect to self-built server using WiFi, but cannot connect in 4G mode 🔐

Reason: Xiage's firmware requires secure connections in 4G mode.

Solutions: There are currently two methods to solve this. Choose either one:

1. Modify the code. Refer to this video solution: https://www.bilibili.com/video/BV18MfTYoE85

2. Use nginx to configure SSL certificates. Refer to tutorial: https://icnt94i5ctj4.feishu.cn/docx/GnYOdMNJOoRCljx1ctecsj9cnRe

### 5. How to improve Xiaozhi's dialogue response speed? ⚡

This project is configured as a low-cost solution by default. It's recommended that beginners first use the default free models to solve the "can run" problem, then optimize for "runs fast".  
To improve response speed, you can try replacing various components. Starting from version `0.5.2`, the project supports streaming configuration, which improves response speed by approximately `2.5 seconds` compared to earlier versions, significantly enhancing user experience.

| Module Name | Entry-level Free Setup | Streaming Configuration |
|:---:|:---:|:---:|
| ASR (Speech Recognition) | FunASR (Local) | 👍FunASR (Local GPU Mode) |
| LLM (Large Language Model) | ChatGLMLLM (Zhipu glm-4-flash) | 👍AliLLM (qwen3-235b-a22b-instruct-2507) or 👍DoubaoLLM (doubao-1-5-pro-32k-250115) |
| VLLM (Vision Large Model) | ChatGLMVLLM (Zhipu glm-4v-flash) | 👍QwenVLVLM (Qwen qwen2.5-vl-3b-instructh) |
| TTS (Text-to-Speech) | ✅LinkeraiTTS (Lingxi Streaming) | 👍HuoshanDoubleStreamTTS (Volcano Dual-stream Speech Synthesis) or 👍AliyunStreamTTS (Alibaba Cloud Streaming Speech Synthesis) |
| Intent (Intent Recognition) | function_call (Function Call) | function_call (Function Call) |
| Memory (Memory Function) | mem_local_short (Local Short-term Memory) | mem_local_short (Local Short-term Memory) |

If you care about the time consumption of each component, please refer to the [Xiaozhi Component Performance Test Report](https://github.com/xinnan-tech/xiaozhi-performance-research). You can conduct actual tests in your environment according to the test methods in the report.

### 6. I speak slowly, Xiaozhi always interrupts when I pause 🗣️

Suggestion: Find the following section in the configuration file and increase the value of `min_silence_duration_ms` (for example, change it to `1000`):

```yaml
VAD:
  SileroVAD:
    threshold: 0.5
    model_dir: models/snakers4_silero-vad
    min_silence_duration_ms: 700  # If you have long pauses when speaking, increase this value
```

### 7. Deployment Related Tutorials
1. [How to perform minimal deployment](./Deployment.md)<br/>
2. [How to perform full module deployment](./Deployment_all.md)<br/>
2. [How to deploy MQTT gateway to enable MQTT+UDP protocol](./mqtt-gateway-integration.md)<br/>
3. [How to automatically pull the latest code of this project for automatic compilation and startup](./dev-ops-integration.md)<br/>
4. [How to integrate with Nginx](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues/791)<br/>

### 8. Firmware Compilation Related Tutorials
1. [How to compile Xiaozhi firmware yourself](./firmware-build.md)<br/>
2. [How to modify OTA address based on Xiage's compiled firmware](./firmware-setting.md)<br/>

### 8. Extension Related Tutorials
1. [How to enable mobile phone number registration for smart control panel](./ali-sms-integration.md)<br/>
2. [How to integrate HomeAssistant for smart home control](./homeassistant-integration.md)<br/>
3. [How to enable vision models for photo recognition](./mcp-vision-integration.md)<br/>
4. [How to deploy MCP endpoints](./mcp-endpoint-enable.md)<br/>
5. [How to access MCP endpoints](./mcp-endpoint-integration.md)<br/>
6. [How to enable voiceprint recognition](./voiceprint-integration.md)<br/>
10. [News plugin source configuration guide](./newsnow_plugin_config.md)<br/>

### 9. Voice Cloning and Local Voice Deployment Related Tutorials
1. [How to clone voice tones in the smart control panel](./huoshan-streamTTS-voice-cloning.md)<br/>
2. [How to deploy and integrate index-tts local voice](./index-stream-integration.md)<br/>
3. [How to deploy and integrate fish-speech local voice](./fish-speech-integration.md)<br/>
4. [How to deploy and integrate PaddleSpeech local voice](./paddlespeech-deploy.md)<br/>

### 10. Performance Testing Tutorials
1. [Component speed testing guide](./performance_tester.md)<br/>
2. [Regularly published test results](https://github.com/xinnan-tech/xiaozhi-performance-research)<br/>

### 13. For more questions, please contact us for feedback 💬

You can submit your questions in [issues](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues).