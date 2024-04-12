# Enligsh/[中文](./README_zh.md)

# io-net-setup-mac

This document describes how to setup IO net node on a mac host(with m1 or m2 cpu), in a much simpler way, withou needing to install docker desktop(huge and laggy on mac). We can complete all the steps in our terminal.
In this doc, we use colima, an alternative linux vm engine on mac os, to provide the linux runtime for docker.

# Prerequisites
 - Have a Mac machine(with m1 or m2 cpu).
 - Have an io.net account and walk throught the worker setup flow on io.net. Steps:
    1. Go to [io.net](https://io.net/) and `Sign up`(top right corner).
    2. Go to https://cloud.io.net/worker/devices and click `Connect New Worker`(top right corner).
    3. Fill up the form until the step "Download and launch binary" shows up.

## Steps
This guide doesn't use docker-desktop, which is recommended by the offical steps, but uses a light-weight Linux runtime provider, called [https://github.com/abiosoft/colima](https://github.com/abiosoft/colima).

### Open Terminal on Mac
Search for "terminal" app and open it. all the following setps will be perfomed in the terminal.

### Installing Dependencies
```bash
brew install colima
brew install docker # this is not docker desktop. It's only the docker command line tool.
```

### Start the Linux Runtime
```bash
# we allocate this much resource because we might get some jobs from io.net, and the image itself is already very big
colima start --cpu 8 --memory 16 --disk 50
```

### Download the io.net Binary
```bash
curl -L https://github.com/ionet-official/io_launch_binaries/raw/main/launch_binary_mac -o launch_binary_mac
chmod +x launch_binary_mac
```

### Run the Container to Setup the Worker
Copy the command in the step "3. Run the command to connect device", from the "Add New Devices" form in your broswer(the one we followed in the prerequisites section).
It will be in the following format:
`./launch_binary_mac --device_id=xxxxxx --user_id=xxxxx --operating_system="macOS" --usegpus=false --device_name=xxxxx`
Every time you setup a new decive, the command will be different.

Paste the copied command to your terminal and hit enter. Wait for the command to return.

### Verify Your Containers are Up
After 5-10 minutes, use the following command to check your container status:
```bash
docker ps
```
You should see 2 containers running, like this(the `CONTAINER ID` and `NAMES` columns will be different):
```
CONTAINER ID   IMAGE                               COMMAND                  CREATED             STATUS             PORTS     NAMES
d4b2059f4060   ionetcontainers/io-worker-monitor   "tail -f /dev/null"      About an hour ago   Up About an hour             goofy_vaughan
a81cc2d5f904   ionetcontainers/io-worker-vc        "sudo -E /srp/invoke…"   About an hour ago   Up About an hour             friendly_meninsky
```
