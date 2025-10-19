# MCP Endpoint Deployment Usage Guide

This tutorial contains 3 parts:
- 1. How to deploy the MCP endpoint service
- 2. How to configure MCP endpoints during full module deployment
- 3. How to configure MCP endpoints during single module deployment

# 1. How to Deploy the MCP Endpoint Service

## Step 1: Download MCP Endpoint Project Source Code

Open the [MCP endpoint project address](https://github.com/xinnan-tech/mcp-endpoint-server) in your browser.

After opening, find a green button on the page labeled `Code`, click on it, and then you will see the `Download ZIP` button.

Click it to download the project source code archive. After downloading to your computer, extract it. At this time, its name might be `mcp-endpoint-server-main`. You need to rename it to `mcp-endpoint-server`.

## Step 2: Start the Program
This project is a very simple project, and it's recommended to run it using Docker. However, if you don't want to use Docker, you can refer to [this page](https://github.com/xinnan-tech/mcp-endpoint-server/blob/main/README_dev.md) to run from source code. The following is the Docker running method:

```
# Enter the project source code root directory
cd mcp-endpoint-server

# Clear cache
docker compose -f docker-compose.yml down
docker stop mcp-endpoint-server
docker rm mcp-endpoint-server
docker rmi ghcr.nju.edu.cn/xinnan-tech/mcp-endpoint-server:latest

# Start docker container
docker compose -f docker-compose.yml up -d
# View logs
docker logs -f mcp-endpoint-server
```

At this time, the logs will output similar logs as follows:
```
250705 INFO-=====The following addresses are control panel/single module MCP endpoint addresses====
250705 INFO-Control panel MCP parameter configuration: http://172.22.0.2:8004/mcp_endpoint/health?key=abc
250705 INFO-Single module deployment MCP endpoint: ws://172.22.0.2:8004/mcp_endpoint/mcp/?token=def
250705 INFO-=====Please choose according to specific deployment, do not leak to anyone======
```

Please copy the two interface addresses:

Since you are using Docker deployment, you must not use the above addresses directly!

Since you are using Docker deployment, you must not use the above addresses directly!

Since you are using Docker deployment, you must not use the above addresses directly!

First copy the addresses and put them in a draft. You need to know what your computer's LAN IP is. For example, if my computer's LAN IP is `192.168.1.25`, then my original interface addresses:
```
Control panel MCP parameter configuration: http://172.22.0.2:8004/mcp_endpoint/health?key=abc
Single module deployment MCP endpoint: ws://172.22.0.2:8004/mcp_endpoint/mcp/?token=def
```
need to be changed to:
```
Control panel MCP parameter configuration: http://192.168.1.25:8004/mcp_endpoint/health?key=abc
Single module deployment MCP endpoint: ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=def
```

After making the changes, please use your browser to directly access the `Control panel MCP parameter configuration`. When the browser shows code similar to this, it means success:
```
{"result":{"status":"success","connections":{"tool_connections":0,"robot_connections":0,"total_connections":0}},"error":null,"id":null,"jsonrpc":"2.0"}
```

Please keep the above two `interface addresses` well, they will be needed in the next step.

# 2. How to Configure MCP Endpoints During Full Module Deployment

If you are using full module deployment, use the administrator account to log into the control panel, click `Parameter Dictionary` at the top, and select the `Parameter Management` function.

Then search for the parameter `server.mcp_endpoint`. At this time, its value should be `null`.
Click the modify button and paste the `Control panel MCP parameter configuration` obtained from the previous step into the `Parameter Value`. Then save.

If it can be saved successfully, it means everything is going well, and you can go to the agent to check the effect. If it's not successful, it means the control panel cannot access the MCP endpoint, most likely due to network firewall or incorrect LAN IP address.

# 3. How to Configure MCP Endpoints During Single Module Deployment

If you are using single module deployment, find your configuration file `data/.config.yaml`.
Search for `mcp_endpoint` in the configuration file. If not found, add the `mcp_endpoint` configuration. Similar to how I did it:
```
server:
  websocket: ws://your_ip_or_domain:port/xiaozhi/v1/
  http_port: 8002
log:
  log_level: INFO

# There may be more configurations here..

mcp_endpoint: your_endpoint_websocket_address
```
At this time, please paste the `Single module deployment MCP endpoint` obtained from `How to deploy the MCP endpoint service` into `mcp_endpoint`. Like this:

```
server:
  websocket: ws://your_ip_or_domain:port/xiaozhi/v1/
  http_port: 8002
log:
  log_level: INFO

# There may be more configurations here

mcp_endpoint: ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=def
```

After configuration, starting the single module will output the following logs:
```
250705[__main__]-INFO-Initialize component: vad success SileroVAD
250705[__main__]-INFO-Initialize component: asr success FunASRServer
250705[__main__]-INFO-OTA interface is          http://192.168.1.25:8002/xiaozhi/ota/
250705[__main__]-INFO-Vision analysis interface is     http://192.168.1.25:8002/mcp/vision/explain
250705[__main__]-INFO-mcp endpoint is        ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
250705[__main__]-INFO-Websocket address is    ws://192.168.1.25:8000/xiaozhi/v1/
250705[__main__]-INFO-=======The above address is websocket protocol address, do not access with browser=======
250705[__main__]-INFO-If you want to test websocket please use Google Chrome to open test_page.html in test directory
250705[__main__]-INFO-=============================================================
```

As above, if you can output similar `mcp endpoint is` with `ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc`, it means the configuration is successful.

