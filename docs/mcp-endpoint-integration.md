# MCP Endpoint Usage Guide

This tutorial uses the open-source MCP calculator function as an example to introduce how to integrate your own custom MCP service into your own endpoint.

The prerequisite for this tutorial is that your `xiaozhi-server` has enabled the MCP endpoint functionality. If you haven't enabled it yet, you can first enable it according to [this tutorial](./mcp-endpoint-enable.md).

# How to Integrate a Simple MCP Function for an Agent, Such as Calculator Function

### If You Are Using Full Module Deployment
If you are using full module deployment, you can enter the Smart Control Panel, Agent Management, click `Configure Role`, and on the right side of `Intent Recognition`, there is an `Edit Functions` button.

Click this button. In the popup page, at the bottom, there will be `MCP Endpoint`. Normally, it will display the `MCP Endpoint Address` of this agent. Next, we will extend a calculator function based on MCP technology for this agent.

This `MCP Endpoint Address` is very important, you will use it later.

### If You Are Using Single Module Deployment
If you are using single module deployment and you have configured the MCP endpoint address in the configuration file, then normally, when the single module deployment starts, it will output the following logs.
```
250705[__main__]-INFO-Initialize component: vad successful SileroVAD
250705[__main__]-INFO-Initialize component: asr successful FunASRServer
250705[__main__]-INFO-OTA Interface:        http://192.168.1.25:8002/xiaozhi/ota/
250705[__main__]-INFO-Vision Analysis Interface: http://192.168.1.25:8002/mcp/vision/explain
250705[__main__]-INFO-MCP Endpoint:       ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
250705[__main__]-INFO-Websocket Address:  ws://192.168.1.25:8000/xiaozhi/v1/
250705[__main__]-INFO-=======The above addresses are websocket protocol addresses, do not access with browser=======
250705[__main__]-INFO-To test websocket, please use Chrome browser to open test_page.html in the test directory
250705[__main__]-INFO-=============================================================
```

As shown above, the output `MCP Endpoint:` shows `ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc` which is your `MCP Endpoint Address`.

This `MCP Endpoint Address` is very important, you will use it later.

## Step 1: Download MCP Calculator Project Code

Open the [calculator project](https://github.com/78/mcp-calculator) written by XiaGe in your browser.

After opening, find a green button on the page labeled `Code`, click on it, and then you will see the `Download ZIP` button.

Click it to download the project source code archive. After downloading to your computer, extract it. At this time, its name might be `mcp-calculator-main`. You need to rename it to `mcp-calculator`. Next, we use command line to enter the project directory and install dependencies.

```bash
# Enter project directory
cd mcp-calculator

conda remove -n mcp-calculator --all -y
conda create -n mcp-calculator python=3.10 -y
conda activate mcp-calculator

pip install -r requirements.txt
```

## Step 2: Start

Before starting, first copy the MCP endpoint address from your agent in the Smart Control Panel.

For example, my agent's MCP address is
```
ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
```

Start inputting commands

```bash
export MCP_ENDPOINT=ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
```

After inputting, start the program

```bash
python mcp_pipe.py calculator.py
```

### If You Are Using Smart Control Panel Deployment
If you are using Smart Control Panel deployment, after starting, you can enter the Smart Control Panel, click refresh MCP connection status, and you will see your extended function list.

### If You Are Using Single Module Deployment
If you are using single module deployment, when the device connects, it will output similar logs, indicating success

```
250705 -INFO-Initializing MCP Endpoint: wss://2662r3426b.vicp.fun/mcp_e 
250705 -INFO-Sending MCP Endpoint initialization message
250705 -INFO-MCP Endpoint connection successful
250705 -INFO-MCP Endpoint initialization successful
250705 -INFO-Unified tool handler initialization complete
250705 -INFO-MCP Endpoint server info: name=Calculator, version=1.9.4
250705 -INFO-MCP Endpoint supported tool count: 1
250705 -INFO-All MCP Endpoint tools acquired, client ready
250705 -INFO-Tool cache refreshed
250705 -INFO-Currently supported function list: [ 'get_time', 'get_lunar', 'play_music', 'get_weather', 'handle_exit_intent', 'calculator']
```
If it contains `'calculator'`, it means the device will be able to call the calculator tool based on intent recognition.