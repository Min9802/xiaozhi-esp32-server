# Smart Control Panel Volcano Dual-Stream Speech Synthesis + Voice Cloning Configuration Tutorial

This tutorial is divided into 4 stages: Preparation stage, Configuration stage, Cloning stage, and Usage stage. It mainly introduces the process of configuring Volcano dual-stream speech synthesis + voice cloning through the smart control panel.

## Stage 1: Preparation Stage
The super administrator should first activate the Volcano Engine service and obtain the App Id and Access Token. By default, Volcano Engine will provide one voice resource. This voice resource needs to be copied to this project.

If you want to clone multiple voices, you need to purchase and activate multiple voice resources. Just copy the voice ID (S_xxxxx) of each voice resource to this project and then assign it to system accounts for use. Here are the detailed steps:

### 1. Activate Volcano Engine Service
Visit https://console.volcengine.com/speech/app to create an application in application management, and check the Speech Synthesis Large Model and Voice Cloning Large Model.

### 2. Get Voice Resource ID
Visit https://console.volcengine.com/speech/service/9999 to copy three items: App Id, Access Token, and Voice ID (S_xxxxx). As shown in the figure:

![Get Voice Resources](images/image-clone-integration-01.png)

## Stage 2: Configure Volcano Engine Service

### 1. Fill in Volcano Engine Configuration

Log in to the smart control panel with the super administrator account, click [Model Configuration] at the top, then click [Speech Synthesis] on the left side of the model configuration page. Search for "Volcano Dual-Stream Speech Synthesis", click modify, enter your Volcano Engine `App Id` in the [Application ID] field, and enter the `Access Token` in the [Access Token] field. Then save.

### 2. Assign Voice Resource ID to System Account

Log in to the smart control panel with the super administrator account, click [Voice Cloning] and [Voice Resources] at the top.

Click the add button, select "Volcano Dual-Stream Speech Synthesis" in [Platform Name];

Enter your Volcano Engine voice resource ID (S_xxxxx) in [Voice Resource ID], press Enter after entering;

Select the system account you want to assign to in [Belonging Account], you can assign it to yourself. Then click save

## Stage 3: Cloning Stage

If after logging in, you click [Voice Cloning] » [Voice Cloning] at the top and see [Your account currently has no voice resources, please contact the administrator to allocate voice resources], it means you haven't assigned the voice resource ID to this account in Stage 2. Go back to Stage 2 and assign voice resources to the corresponding account.

If after logging in, you click [Voice Cloning] » [Voice Cloning] at the top and can see the corresponding voice list, please continue.

You will see the corresponding voice list in the list. Select one of the voice resources and click the [Upload Audio] button. After uploading, you can preview the sound or extract a certain segment of sound. After confirmation, click the [Upload Audio] button.
![Upload Audio](images/image-clone-integration-02.png)

After uploading audio, you will see the corresponding voice in the list change to "Pending Cloning" status. Click the [Clone Now] button. Wait 1-2 seconds for the result.

If cloning fails, hover your mouse over the "Error Information" icon to display the reason for failure.

If cloning is successful, you will see the corresponding voice in the list change to "Training Successful" status. At this time, you can click the modify button in the [Voice Name] column to modify the name of the voice resource for easy selection and use later.

## Stage 4: Usage Stage

Click [Agent Management] at the top, select any agent, and click the [Configure Role] button.

For Text-to-Speech (TTS), select "Volcano Dual Stream TTS". In the list, find the timbre resource with "Clone Timbre" in the name (as shown in the figure), select it, and click save.
![Select Timbre](images/image-clone-integration-03.png)

Next, you can wake up Xiaozhi and have a conversation with it.
