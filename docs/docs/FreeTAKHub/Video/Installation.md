# Video Server
MediaMTX is a RTSP / RTPM capable software to stream in real time from / to TAK devices.
It's used with the [FreeTAK UAS](https://github.com/FreeTAKTeam/FreeTAKServer-User-Docs/blob/main/docs/docs/tools/FreeTAKUAS.md) and [TAK ICU](https://github.com/FreeTAKTeam/FreeTAKServer-User-Docs/blob/main/docs/docs/tools/takICU.md)

## Installation
Get the linux_amd64 installation package link  from the [release page](https://github.com/aler9/mediamtx/releases)
![image](https://user-images.githubusercontent.com/60719165/142771721-3479eda5-5a0c-49a3-ba34-f0970bd4882d.png)

Download the MediaMTX release:
```
wget https://github.com/aler9/mediamtx/releases/download/v0.22.2/mediamtx_v0.22.2_linux_amd64.tar.gz
```

Un-tar the package:
```
tar -zxvf mediamtx_v0.22.2_linux_amd64.tar.gz
```


Edit the YAML file
![image](https://user-images.githubusercontent.com/60719165/142767943-a3363aec-a250-4b02-9156-3b9a58627665.png)

- Enable API by setting yes
- Support protocols only TCP
- Set the  API address 0.0.0.0

![image](https://user-images.githubusercontent.com/60719165/142767998-72a03e49-9055-4d4e-ac90-e8e00c51ffa9.png)

### Configure to start as a service
To start the video server as a system daemon, we will create a systemd unit file. The systemd framework is the service manager used by Ubuntu, Debian and many other Linux distributions. By creating a unit file to manage the daemon, systemd will ensure the service is started with the OS and stays healthy.

Move the executable:

```
sudo mv mediamtx /usr/local/bin/
```
Move the configuration file:
```
sudo mv mediamtx.yml /usr/local/etc/
```

Create the Unit File. Copy this complete text and paste into the console:
```
sudo tee /etc/systemd/system/mediamtx.service >/dev/null << EOF
[Unit]
After=network.target
[Service]
ExecStart=/usr/local/bin/mediamtx /usr/local/etc/mediamtx.yml
[Install]
WantedBy=multi-user.target
EOF
```

Start the service:
```
sudo systemctl start mediamtx.service
```

Enable  the service:
```
sudo systemctl enable mediamtx.service
```
