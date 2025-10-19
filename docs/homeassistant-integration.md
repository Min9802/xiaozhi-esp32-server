# Xiaozhi ESP32 Open Source Server and HomeAssistant Integration Guide

[TOC]

-----

## Introduction

This document will guide you on how to integrate ESP32 devices with HomeAssistant.

## Prerequisites

- `HomeAssistant` has been installed and configured
- The model I chose for this time is: free ChatGLM, which supports function call functionality

## Pre-operation (Required)

### 1. Get HA's Network Address Information

Please visit your Home Assistant's network address. For example, my HA address is 192.168.4.7, and the port is the default 8123, so open in browser:

```
http://192.168.4.7:8123
```

> Manual method to query HA's IP address **(Only applicable when xiaozhi esp32-server and HA are deployed on the same network device [e.g., same WiFi])**:
>
> 1. Enter Home Assistant (frontend).
>
> 2. Click **Settings** in the bottom left → **System** → **Network**.
>
> 3. Scroll to the bottom `Home Assistant URL` area, in the `local network` section, click the `eye` button to see the currently used IP address (e.g., `192.168.1.10`) and network interface. Click `copy link` to copy directly.
>
>    ![image-20250504051716417](images/image-ha-integration-01.png)

Or, if you have already set up a directly accessible Home Assistant OAuth address, you can also access it directly in the browser:

```
http://homeassistant.local:8123
```

### 2. Log into `Home Assistant` to Get Development Key

Log into `HomeAssistant`, click `bottom left avatar -> Profile`, switch to the `Security` tab, scroll to the bottom to `Long-lived access tokens` to generate an api_key, and copy and save it. All subsequent methods will need to use this api key and it only appears once (tip: You can save the generated QR code image to scan and extract the api key later).

## Method 1: Xiaozhi Community Co-built HA Call Function

### Function Description

- If you need to add new devices later, this method requires manually restarting the `xiaozhi-esp32-server service` to update device information **(Important)**.

- You need to ensure that you have integrated `Xiaomi Home` in HomeAssistant and imported Mijia devices into `HomeAssistant`.

- You need to ensure that the `xiaozhi-esp32-server control panel` can be used normally.

- My `xiaozhi-esp32-server control panel` and `HomeAssistant` are deployed on the same machine on different ports, version is `0.3.10`

  ```
  http://192.168.4.7:8002
  ```


### Configuration Steps

#### 1. Log into `HomeAssistant` to Organize Device List to Control

Log into `HomeAssistant`, click `Settings` in the bottom left, then enter `Devices & Services`, and click `Entities` at the top.

Then search for the switches you want to control in entities. After results appear, click on one of the results in the list, and a switch interface will appear.

In the switch interface, try clicking the switch to see if it turns on/off with our clicks. If it can be operated, it means the network connection is normal.

Then find the settings button in the switch panel, click it, and you can view this switch's `Entity ID`.

Open a notepad and organize one piece of data in this format:

Location + English comma + Device name + English comma + `Entity ID` + English semicolon

For example, I'm at the company, I have a toy lamp, and its identifier is switch.cuco_cn_460494544_cp1_on_p_2_1, then I would write this data like this:

```
Company,Toy Light,switch.cuco_cn_460494544_cp1_on_p_2_1;
```

Of course, I might eventually need to operate two lights, so my final result would be:

```
Company,Toy Light,switch.cuco_cn_460494544_cp1_on_p_2_1;
Company,Desk Lamp,switch.iot_cn_831898993_socn1_on_p_2_1;
```

We call this string the "Device List String" and it needs to be saved well, as it will be useful later.

#### 2. Log into `Control Panel`

![image-20250504051716417](images/image-ha-integration-06.png)

Use the administrator account to log into the `Control Panel`. In `Agent Management`, find your agent and click `Configure Role`.

Set intent recognition to `Function Call` or `LLM Intent Recognition`. At this point you will see an `Edit Functions` option on the right. Click the `Edit Functions` button, and a `Function Management` dialog will pop up.

In the `Function Management` dialog, you need to check `HomeAssistant Device Status Query` and `HomeAssistant Device Status Modification`.

After checking, click `HomeAssistant Device Status Query` in `Selected Functions`, then configure your `HomeAssistant` address, key, and device list string in `Parameter Configuration`.

After editing, click `Save Configuration`. The `Function Management` dialog will hide, and then you can click to save the agent configuration.

After successful saving, you can wake up the device for operation.

#### 3. Wake Up Device for Control

Try saying to the esp32, "Turn on XXX light"

## Method 2: Xiaozhi Uses Home Assistant Voice Assistant as LLM Tool

### Function Description

- This method has a serious drawback—**this method cannot use the function_call plugin capabilities of the Xiaozhi open source ecosystem**, because using Home Assistant as Xiaozhi's LLM tool transfers intent recognition capability to Home Assistant. However, **this method can experience native Home Assistant operation functions while Xiaozhi's chat capability remains unchanged**. If you really mind this, you can use [Method 3](#method-3-using-home-assistants-mcp-service-recommended) which is also supported by Home Assistant and can experience Home Assistant's functionality to the greatest extent.

### Configuration Steps:

#### 1. Configure Home Assistant's Large Model Voice Assistant.

**You need to configure Home Assistant's voice assistant or large model tool in advance.**

#### 2. Get Home Assistant's Language Assistant Agent ID.

1. Enter the Home Assistant page. Click `Developer Tools` on the left side.
2. In the opened `Developer Tools`, click the `Actions` tab (as shown in operation 1), in the page's `Actions` option bar, find or enter `conversation.process` and select `Conversation: Process` (as shown in operation 2).

![image-20250504043539343](images/image-ha-integration-02.png)

3. Check the `Agent` option on the page, and in the now-active `Conversation Agent` field, select the voice assistant name you configured in step 1. As shown in the figure, I have configured `ZhipuAi` and selected it.

![image-20250504043854760](images/image-ha-integration-03.png)

4. After selection, click `Go to YAML mode` at the bottom left of the form.

![image-20250504043951126](images/image-ha-integration-04.png)

5. Copy the agent-id value, for example, mine in the figure is `01JP2DYMBDF7F4ZA2DMCF2AGX2` (for reference only).

![image-20250504044046466](images/image-ha-integration-05.png)

6. Switch to the `config.yaml` file of the Xiaozhi open source server `xiaozhi-esp32-server`, in the LLM configuration, find Home Assistant, and set your Home Assistant's network address, API key, and the agent_id you just queried.
7. Modify the `LLM` in the `selected_module` property of the `config.yaml` file to `HomeAssistant`, and `Intent` to `nointent`.
8. Restart the Xiaozhi open source server `xiaozhi-esp32-server` to use normally.

## Method 3: Using Home Assistant's MCP Service (Recommended)

### Function Description

- You need to integrate and install the HA integration in advance in Home Assistant—[Model Context Protocol Server](https://www.home-assistant.io/integrations/mcp_server/).

- This method and Method 2 are both solutions officially provided by HA. Unlike Method 2, you can normally use the open source co-built plugins of the Xiaozhi open source server `xiaozhi-esp32-server`, while allowing you to freely use any LLM large model that supports function_call functionality.

### Configuration Steps

#### 1. Install Home Assistant's MCP Service Integration.

Official integration website—[Model Context Protocol Server](https://www.home-assistant.io/integrations/mcp_server/).

Or follow the manual operations below.

> - Go to Home Assistant's **[Settings > Devices & Services](https://my.home-assistant.io/redirect/integrations)**.
>
> - In the bottom right corner, select the **[Add Integration](https://my.home-assistant.io/redirect/config_flow_start?domain=mcp_server)** button.
>
> - Select **Model Context Protocol Server** from the list.
>
> - Follow the on-screen instructions to complete the setup.

#### 2. Configure Xiaozhi Open Source Server MCP Configuration Information

Enter the `data` directory and find the `.mcp_server_settings.json` file.

If there is no `.mcp_server_settings.json` file in your `data` directory:
- Please copy the `mcp_server_settings.json` file from the `xiaozhi-server` folder root directory to the `data` directory and rename it to `.mcp_server_settings.json`
- Or [download this file](https://github.com/xinnan-tech/xiaozhi-esp32-server/blob/main/main/xiaozhi-server/mcp_server_settings.json), download it to the `data` directory, and rename it to `.mcp_server_settings.json`

Modify this part of the content in `"mcpServers"`:

```json
"Home Assistant": {
      "command": "mcp-proxy",
      "args": [
        "http://YOUR_HA_HOST/mcp_server/sse"
      ],
      "env": {
        "API_ACCESS_TOKEN": "YOUR_API_ACCESS_TOKEN"
      }
},
```

Note:

1. **Replace Configuration:**
   - Replace `YOUR_HA_HOST` in `args` with your HA service address. If your service address already contains https/http (e.g., `http://192.168.1.101:8123`), you only need to fill in `192.168.1.101:8123`.
   - Replace `YOUR_API_ACCESS_TOKEN` in `API_ACCESS_TOKEN` under `env` with the development key api key you obtained earlier.
2. **If you add configuration within the `"mcpServers"` brackets and there are no subsequent new `mcpServers` configurations, you need to remove the final comma `,`**, otherwise parsing may fail.

**Final effect reference (as follows):**

```json
 "mcpServers": {
    "Home Assistant": {
      "command": "mcp-proxy",
      "args": [
        "http://192.168.1.101:8123/mcp_server/sse"
      ],
      "env": {
        "API_ACCESS_TOKEN": "abcd.efghi.jkl"
      }
    }
  }
```

#### 3. Configure Xiaozhi Open Source Server System Configuration

1. **Choose any LLM large model that supports function_call as Xiaozhi's LLM chat assistant (but do not choose Home Assistant as the LLM tool)**. The model I chose this time is: free ChatGLM, which supports function call functionality, but sometimes the calls are not very stable. If you want to pursue stability, it's recommended to set the LLM to: DoubaoLLM, with the specific model_name being: doubao-1-5-pro-32k-250115.

2. Switch to the `config.yaml` file of the Xiaozhi open source server `xiaozhi-esp32-server`, set your LLM large model configuration, and adjust the `Intent` in the `selected_module` configuration to `function_call`.

3. Restart the Xiaozhi open source server `xiaozhi-esp32-server` to use normally.