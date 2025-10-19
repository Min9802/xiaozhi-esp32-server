# Weather Plugin Usage Guide

## Overview

The weather plugin `get_weather` is one of the core functions of the Xiaozhi ESP32 voice assistant, supporting voice queries for weather information across the country. The plugin is based on the QWeather API and provides real-time weather and 7-day weather forecast functions.

## API Key Application Guide

### 1. Register QWeather Account

1. Visit [QWeather Console](https://console.qweather.com/)
2. Register an account and complete email verification
3. Log in to the console

### 2. Create Application to Get API Key

1. After entering the console, click ["Project Management"](https://console.qweather.com/project?lang=zh) on the right → "Create Project"
2. Fill in project information:
   - **Project Name**: Such as "Xiaozhi Voice Assistant"
3. Click save
4. After the project is created, click "Create Credentials" in that project
5. Fill in credential information:
    - **Credential Name**: Such as "Xiaozhi Voice Assistant"
    - **Authentication Method**: Select "API Key"
6. Click save
7. Copy the `API Key` from the credentials, this is the first key configuration information

### 3. Get API Host

1. In the console, click ["Settings"](https://console.qweather.com/setting?lang=zh) → "API Host"
2. View the dedicated `API Host` address assigned to you, this is the second key configuration information

The above operations will provide two important configuration information: `API Key` and `API Host`

## Configuration Methods (Choose One)

### Method 1. If You Use Smart Control Panel Deployment (Recommended)

1. Log in to Smart Control Panel
2. Enter "Role Configuration" page
3. Select the agent to configure
4. Click the "Edit Functions" button
5. Find the "Weather Query" plugin in the right parameter configuration area
6. Check "Weather Query"
7. Fill the first key configuration `API Key` you copied into `Weather Plugin API Key`
8. Fill the second key configuration `API Host` you copied into `Developer API Host`
9. Save configuration, then save agent configuration

### Method 2. If You Only Use Single Module xiaozhi-server Deployment

Configure in `data/.config.yaml`:

1. Fill the first key configuration `API Key` you copied into `api_key`
2. Fill the second key configuration `API Host` you copied into `api_host`
3. Fill your city into `default_location`, for example `Guangzhou`

```yaml
plugins:
  get_weather:
    api_key: "your_qweather_api_key"
    api_host: "your_qweather_api_host"
    default_location: "your_default_query_city"
```

