# Full Module Source Code Deployment Auto-Upgrade Method

This tutorial is for full module source code deployment enthusiasts, showing how to automatically pull source code, compile, and start ports through automatic commands to achieve the most efficient system upgrade.

This project's test platform `https://2662r3426b.vicp.fun` has been using this method since it opened, with good results.

You can refer to the video tutorial published by Bilibili blogger `毕乐labs`: [《Open Source Xiaozhi Server xiaozhi-server Auto Update and Latest Version MCP Endpoint Configuration Tutorial》](https://www.bilibili.com/video/BV15H37zHE7Q)

# Prerequisites
- Your computer/server runs Linux operating system
- You have already successfully run through the entire process
- You like to follow the latest features but find manual deployment troublesome each time, and expect an automatic update method

The second condition must be met because some files involved in this tutorial, such as JDK, Node.js environment, Conda environment, etc., are only available after you have successfully run through the entire process. If you haven't run through it, you might not understand what I mean when I mention certain files.

# Tutorial Effects
- Solve the problem of not being able to pull the latest project source code in China
- Automatically pull code and compile frontend files
- Automatically pull code and compile Java files, automatically kill port 8002, automatically start port 8002
- Automatically pull Python code, automatically kill port 8000, automatically start port 8000

# Step 1: Choose Your Project Directory

For example, I planned my project directory to be a newly created blank directory. If you don't want errors, you can use the same as me:
```
/home/system/xiaozhi
```

# Step 2: Clone This Project
At this moment, first execute the first command to pull the source code. This command is suitable for domestic network servers and computers, no VPN required:

```
cd /home/system/xiaozhi
git clone https://ghproxy.net/https://github.com/xinnan-tech/xiaozhi-esp32-server.git
```

After execution, your project directory will have an additional folder `xiaozhi-esp32-server`, which is the project source code.

# Step 3: Copy Basic Files

If you have already successfully run through the entire process, you should be familiar with the funasr model file `xiaozhi-server/models/SenseVoiceSmall/model.pt` and your private configuration file `xiaozhi-server/data/.config.yaml`.

Now you need to copy the `model.pt` file to the new directory, you can do this:
```
# Create the required directories
mkdir -p /home/system/xiaozhi/xiaozhi-esp32-server/main/xiaozhi-server/data/

cp your_original_.config.yaml_complete_path /home/system/xiaozhi/xiaozhi-esp32-server/main/xiaozhi-server/data/.config.yaml
cp your_original_model.pt_complete_path /home/system/xiaozhi/xiaozhi-esp32-server/main/xiaozhi-server/models/SenseVoiceSmall/model.pt
```

# Step 4: Create Three Auto-Build Files

## 4.1 Auto-Build manager-web Module
In the `/home/system/xiaozhi/` directory, create a file named `update_8001.sh` with the following content:

```
cd /home/system/xiaozhi/xiaozhi-esp32-server
git fetch --all
git reset --hard
git pull origin main


cd /home/system/xiaozhi/xiaozhi-esp32-server/main/manager-web
npm install
npm run build
rm -rf /home/system/xiaozhi/manager-web
mv /home/system/xiaozhi/xiaozhi-esp32-server/main/manager-web/dist /home/system/xiaozhi/manager-web
```

After saving, execute the permission command:
```
chmod 777 update_8001.sh
```
After execution, continue below.

## 4.2 Auto-Build and Run manager-api Module
In the `/home/system/xiaozhi/` directory, create a file named `update_8002.sh` with the following content:

```
cd /home/system/xiaozhi/xiaozhi-esp32-server
git pull origin main


cd /home/system/xiaozhi/xiaozhi-esp32-server/main/manager-api
rm -rf target
mvn clean package -Dmaven.test.skip=true
cd /home/system/xiaozhi/

# Find the process ID occupying port 8002
PID=$(sudo netstat -tulnp | grep 8002 | awk '{print $7}' | cut -d'/' -f1)

rm -rf /home/system/xiaozhi/xiaozhi-esp32-api.jar
mv /home/system/xiaozhi/xiaozhi-esp32-server/main/manager-api/target/xiaozhi-esp32-api.jar /home/system/xiaozhi/xiaozhi-esp32-api.jar

# Check if process ID is found
if [ -z "$PID" ]; then
  echo "No process found occupying port 8002"
else
  echo "Found process occupying port 8002, process ID: $PID"
  # Kill the process
  kill -9 $PID
  kill -9 $PID
  echo "Killed process $PID"
fi

nohup java -jar xiaozhi-esp32-api.jar --spring.profiles.active=dev &

tail tail -f nohup.out
```

After saving, execute the permission command:
```
chmod 777 update_8002.sh
```
After execution, continue below.

## 4.3 Auto-Build and Run Python Project
In the `/home/system/xiaozhi/` directory, create a file named `update_8000.sh` with the following content:

```
cd /home/system/xiaozhi/xiaozhi-esp32-server
git pull origin main

# Find the process ID occupying port 8000
PID=$(sudo netstat -tulnp | grep 8000 | awk '{print $7}' | cut -d'/' -f1)

# Check if process ID is found
if [ -z "$PID" ]; then
  echo "No process found occupying port 8000"
else
  echo "Found process occupying port 8000, process ID: $PID"
  # Kill the process
  kill -9 $PID
  kill -9 $PID
  echo "Killed process $PID"
fi
cd main/xiaozhi-server
# Initialize conda environment
source ~/.bashrc
conda activate xiaozhi-esp32-server
pip install -r requirements.txt
nohup python app.py >/dev/null &
tail -f /home/system/xiaozhi/xiaozhi-esp32-server/main/xiaozhi-server/tmp/server.log
```

After saving, execute the permission command:
```
chmod 777 update_8000.sh
```
After execution, continue below.

# Daily Updates

After all the above scripts are created, for daily updates, we just need to execute the following commands in sequence to achieve automatic updates and startup:

```
cd /home/system/xiaozhi
# Update and start Java program
./update_8001.sh
# Update web program
./update_8002.sh
# Update and start python program
./update_8000.sh


# To view java logs later, execute the following command
tail -f nohup.out
# To view python logs later, execute the following command
tail -f /home/system/xiaozhi/xiaozhi-esp32-server/main/xiaozhi-server/tmp/server.log
```

# Notes
The test platform `https://2662r3426b.vicp.fun` uses nginx for reverse proxy. Detailed nginx.conf configuration can be [referenced here](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues/791).

## Common Questions

### 1. Why is port 8001 not seen?
Answer: Port 8001 is used in the development environment for running the frontend. If you are deploying on a server, it's not recommended to use `npm run serve` to start port 8001 to run the frontend. Instead, like this tutorial, compile it into HTML files and then use nginx to manage access.

### 2. Do I need to manually update SQL statements with each update?
Answer: No, because the project uses **Liquibase** to manage database versions, which will automatically execute new SQL scripts.