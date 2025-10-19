# Configure Custom Server Based on Xiage's Pre-compiled Firmware

## Step 1: Confirm Version
Flash Xiage's pre-compiled [firmware version 1.6.1 or above](https://github.com/78/xiaozhi-esp32/releases)

## Step 2: Prepare Your OTA Address
If you are using full module deployment according to the tutorial, you should have an OTA address.

At this moment, please open your OTA address with a browser, for example my OTA address:
```
https://2662r3426b.vicp.fun/xiaozhi/ota/
```

If it displays "OTA interface running normally, websocket cluster count: X", then proceed.

If it displays "OTA interface not running properly", it's likely you haven't configured the `Websocket` address in the `Smart Control Panel`. Then:

- 1. Log in to the Smart Control Panel using super administrator

- 2. Click `Parameter Management` in the top menu

- 3. Find the `server.websocket` item in the list and input your `Websocket` address. For example, mine is:

```
wss://2662r3426b.vicp.fun/xiaozhi/v1/
```

After configuration, refresh your OTA interface address with the browser and see if it's normal. If it's still not normal, confirm again whether Websocket has started normally and whether the Websocket address is configured.

## Step 3: Enter Network Configuration Mode
Enter the machine's network configuration mode, click "Advanced Options" at the top of the page, input your server's `OTA` address, click save, and restart the device.
![Please refer to - OTA Address Setting](../docs/images/firmware-setting-ota.png)

## Step 4: Wake Up Xiaozhi and Check Log Output

Wake up Xiaozhi and see if the logs are output normally.


## Frequently Asked Questions
Here are some common questions for reference:

[1. Why does Xiaozhi recognize a lot of Korean, Japanese, and English when I speak?](./FAQ.md)

[2. Why does "TTS task error, file does not exist" appear?](./FAQ.md)

[3. TTS often fails and times out](./FAQ.md)

[4. Can connect to self-built server using WiFi, but cannot connect in 4G mode](./FAQ.md)

[5. How to improve Xiaozhi's dialogue response speed?](./FAQ.md)

[6. I speak slowly, Xiaozhi always interrupts when I pause](./FAQ.md)

[7. I want to use Xiaozhi to control lights, air conditioning, remote power on/off operations](./FAQ.md)
