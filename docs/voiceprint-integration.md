# Voiceprint Recognition Enablement Guide

This tutorial contains 3 parts:
- 1. How to deploy the voiceprint recognition service
- 2. How to configure voiceprint recognition interface during full module deployment
- 3. How to configure voiceprint recognition during minimal deployment

# 1. How to Deploy the Voiceprint Recognition Service

## Step 1: Download Voiceprint Recognition Project Source Code

Open the [voiceprint recognition project address](https://github.com/xinnan-tech/voiceprint-api) in your browser.

After opening, find a green button on the page labeled `Code`, click on it, and then you will see the `Download ZIP` button.

Click it to download the project source code archive. After downloading to your computer, extract it. At this time, its name might be `voiceprint-api-main`
You need to rename it to `voiceprint-api`.

## Step 2: Create Database and Tables

Voiceprint recognition requires a `mysql` database. If you have previously deployed the `Smart Control Panel`, it means you have already installed `mysql`. You can share it.

You can try using the `telnet` command on the host machine to see if you can normally access the `3306` port of `mysql`.
```
telnet 127.0.0.1 3306
```
If you can access port 3306, please ignore the following content and proceed directly to step 3.

If you cannot access it, you need to recall how your `mysql` was installed.

If your mysql was installed using an installation package by yourself, it means your `mysql` has network isolation. You might need to first solve the problem of accessing mysql's `3306` port.

If your `mysql` was installed through this project's `docker-compose_all.yml`, you need to find the `docker-compose_all.yml` file you used to create the database at that time and modify the following content:

Before modification
```
  xiaozhi-esp32-server-db:
    ...
    networks:
      - default
    expose:
      - "3306:3306"
```

After modification
```
  xiaozhi-esp32-server-db:
    ...
    networks:
      - default
    ports:
      - "3306:3306"
```

Note that you need to change `expose` under `xiaozhi-esp32-server-db` to `ports`. After making changes, you need to restart. Here are the commands to restart mysql:

```
# Enter the folder where your docker-compose_all.yml is located, for example mine is xiaozhi-server
cd xiaozhi-server
docker compose -f docker-compose_all.yml down
docker compose -f docker-compose.yml up -d
```

After startup is complete, use the `telnet` command on the host machine again to see if you can normally access mysql's `3306` port.
```
telnet 127.0.0.1 3306
```
Normally this should allow access.

## Step 3: Create Database and Tables
If your host machine can normally access the mysql database, then create a database named `voiceprint_db` and a `voiceprints` table on mysql.

```
CREATE DATABASE voiceprint_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

USE voiceprint_db;

CREATE TABLE voiceprints (
    id INT AUTO_INCREMENT PRIMARY KEY,
    speaker_id VARCHAR(255) NOT NULL UNIQUE,
    feature_vector LONGBLOB NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_speaker_id (speaker_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## Step 4: Configure Database Connection

Enter the `voiceprint-api` folder and create a folder named `data`.

Copy `voiceprint.yaml` from the `voiceprint-api` root directory to the `data` folder and rename it to `.voiceprint.yaml`.

Next, you need to focus on configuring the database connection in `.voiceprint.yaml`.

```
mysql:
  host: "127.0.0.1"
  port: 3306
  user: "root"
  password: "your_password"
  database: "voiceprint_db"
```

Note! Since your voiceprint recognition service is deployed using docker, the `host` needs to be filled in with the `LAN IP of the machine where mysql is located`.

Note! Since your voiceprint recognition service is deployed using docker, the `host` needs to be filled in with the `LAN IP of the machine where mysql is located`.

Note! Since your voiceprint recognition service is deployed using docker, the `host` needs to be filled in with the `LAN IP of the machine where mysql is located`.

## Step 5: Start Program
This project is a very simple project, and it's recommended to run it using docker. However, if you don't want to use docker, you can refer to [this page](https://github.com/xinnan-tech/voiceprint-api/blob/main/README.md) to run it from source code. The following is the docker running method:

```
# Enter this project's source code root directory
cd voiceprint-api

# Clear cache
docker compose -f docker-compose.yml down
docker stop voiceprint-api
docker rm voiceprint-api
docker rmi ghcr.nju.edu.cn/xinnan-tech/voiceprint-api:latest

# Start docker container
docker compose -f docker-compose.yml up -d
# View logs
docker logs -f voiceprint-api
```

At this time, logs similar to the following will be output:
```
250711 INFO-🚀 Start: Production environment service startup (Uvicorn), listening address: 0.0.0.0:8005
250711 INFO-============================================================
250711 INFO-Voiceprint interface address: http://127.0.0.1:8005/voiceprint/health?key=abcd
250711 INFO-============================================================
```

Please copy the voiceprint interface address:

Since you are using docker deployment, you must not use the above address directly!

Since you are using docker deployment, you must not use the above address directly!

First copy the address and put it in a draft. You need to know what your computer's LAN IP is. For example, my computer's LAN IP is `192.168.1.25`, then
the original interface address
```
http://127.0.0.1:8005/voiceprint/health?key=abcd

```
should be changed to
```
http://192.168.1.25:8005/voiceprint/health?key=abcd
```

After modification, please use a browser to directly access the `voiceprint interface address`. When the browser shows code similar to this, it means success.
```
{"total_voiceprints":0,"status":"healthy"}
```

Please keep the modified `voiceprint interface address` as it will be needed in the next step.

# 2. How to Configure Voiceprint Recognition in Full Module Deployment

## Step 1: Configure Interface
If you are using full module deployment, use the administrator account to log into the smart control panel, click `Parameter Dictionary` at the top, and select the `Parameter Management` function.

Then search for the parameter `server.voice_print`. At this time, its value should be `null`.
Click the modify button, paste the `voiceprint interface address` obtained from the previous step into the `Parameter Value`, then save.

If you can save successfully, it means everything is going smoothly and you can go to the agent to check the effect. If unsuccessful, it means the smart control panel cannot access voiceprint recognition, most likely due to network firewall or incorrect LAN IP address.

## Step 2: Set Agent Memory Mode

Enter your agent's role configuration and set memory to `Local Short-term Memory`. Make sure to enable `Report Text + Voice`.

## Step 3: Chat with Your Agent

Power on your device and chat with it using normal speaking speed and tone.

## Step 4: Set Voiceprint

In the smart control panel, on the `Agent Management` page, in the agent's panel, there is a `Voiceprint Recognition` button. Click it. At the bottom there is an `Add Button`. You can register voiceprints for what someone says.
In the pop-up box, it is recommended to fill in the `Description` attribute, which can be this person's profession, personality, hobbies. This helps the agent analyze and understand the speaker.

## Step 3: Chat with Your Agent

Power on your device and ask it, "Do you know who I am?" If it can answer correctly, it means the voiceprint recognition function is working normally.

# 3. How to Configure Voiceprint Recognition in Minimal Deployment

## Step 1: Configure Interface
Open the `xiaozhi-server/data/.config.yaml` file (create it if it doesn't exist), then add/modify the following content:

```
# Voiceprint recognition configuration
voiceprint:
  # Voiceprint interface address
  url: your_voiceprint_interface_address
  # Speaker configuration: speaker_id,name,description
  speakers:
    - "test1,Zhang San,Zhang San is a programmer"
    - "test2,Li Si,Li Si is a product manager"
    - "test3,Wang Wu,Wang Wu is a designer"
```

Paste the `voiceprint interface address` obtained from the previous step into `url`. Then save.

Add `speakers` parameters according to your needs. Note this `speaker_id` parameter, as it will be used later for voiceprint registration.

## Step 2: Register Voiceprint
If you have already started the voiceprint service, you can access `http://localhost:8005/voiceprint/docs` in your local browser to view the API documentation. Here we only explain how to use the voiceprint registration API.

The voiceprint registration API address is `http://localhost:8005/voiceprint/register`, with POST request method.

The request header needs to include Bearer Token authentication, where the token is the part after `?key=` in the `voiceprint interface address`. For example, if my voiceprint registration address is `http://127.0.0.1:8005/voiceprint/health?key=abcd`, then my token is `abcd`.

The request body contains the speaker ID (speaker_id) and WAV audio file (file). Request example:

```
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -F "speaker_id=your_speaker_id_here" \
  -F "file=@/path/to/your/file" \
  http://localhost:8005/voiceprint/register
```

Here `file` is the audio file of the speaker to be registered, and `speaker_id` needs to be consistent with the `speaker_id` configured in step 1. For example, if I need to register Zhang San's voiceprint, and the `speaker_id` for Zhang San in `.config.yaml` is `test1`, then when registering Zhang San's voiceprint, the `speaker_id` in the request body should be `test1`, and `file` should be the audio file of Zhang San speaking.

 ## Step 3: Start Service

Start the Xiaozhi server and voiceprint service to use normally.
