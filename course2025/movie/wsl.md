# **WSL (Windows Subsystem for Linux)**

> [!NOTE]
> This section is optional.  
> You may skip it if you do not plan to use WSL.

---

## **Installing WSL**

1. Open **PowerShell as Administrator**, then run the following command to install WSL:

```powershell
$ wsl --install
Downloading: Windows Subsystem for Linux 2.6.1
Installing: Windows Subsystem for Linux 2.6.1
Windows Subsystem for Linux 2.6.1 has been installed.
Installing Windows optional component: VirtualMachinePlatform

Deployment Image Servicing and Management tool
Version: 10.0.26100.5074

Image Version: 10.0.26100.6584

Enabling feature(s)
[==========================100.0%==========================]
The operation completed successfully.
The requested operation is successful.
Changes will not take effect until the system is rebooted.
```

Alternatively, you can manually enable the WSL feature by running:

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

Once the installation completes, **restart your computer** when prompted.

---

## **Setting the Default WSL Version**

After restarting, open **PowerShell** again and set WSL to use version 1 by default:

```powershell
$ wsl --set-default-version 1
```

> [!NOTE]
> In this course, we use **Ubuntu 24.04 LTS**.  
> It is recommended to install this version for compatibility with class exercises.

---

## **Installing a Linux Distribution**

List all available Linux distributions:

```powershell
$ wsl --list --online
The following is a list of valid distributions that can be installed.
Install using 'wsl.exe --install <Distro>'.

NAME                   FRIENDLY NAME
AlmaLinux-8            AlmaLinux OS 8
AlmaLinux-9            AlmaLinux OS 9
AlmaLinux-Kitten-10    AlmaLinux OS Kitten 10
AlmaLinux-10           AlmaLinux OS 10
Debian                 Debian GNU/Linux
FedoraLinux-42         Fedora Linux 42
Ubuntu                 Ubuntu
Ubuntu-24.04           Ubuntu 24.04 LTS  <-- Recommended for this class
kali-linux             Kali Linux Rolling
openSUSE-Tumbleweed    openSUSE Tumbleweed
openSUSE-Leap-16.0     openSUSE Leap 16.0
Ubuntu-20.04           Ubuntu 20.04 LTS
Ubuntu-22.04           Ubuntu 22.04 LTS
openSUSE-Leap-15.6     openSUSE Leap 15.6
```

Then, install **Ubuntu 24.04 LTS**:

```powershell
$ wsl --install -d Ubuntu-24.04
Downloading: Ubuntu 24.04 LTS
Installing: Ubuntu 24.04 LTS
Distribution successfully installed.
It can be launched via 'wsl.exe -d Ubuntu-24.04'

Launching Ubuntu-24.04...
Provisioning the new WSL instance Ubuntu-24.04
This might take a while...
Create a default Unix user account: phoovich
New password:
Retype new password:
passwd: password updated successfully
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```

When prompted, create your **username** and **password** for the Linux environment.

---

## **Running Bash**

Once installed, you can open the Bash shell by typing:

```powershell
PS C:\Users\phoovich> wsl
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 4.4.0-26100-Microsoft aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu Oct  9 23:51:05 PDT 2025

  System load:           0.52
  Usage of /home:        unknown
  Memory usage:          71%
  Swap usage:            0%
  Processes:             9
  Users logged in:       0
  IPv4 address for eth0: 192.168.66.13
  IPv6 address for eth0: fde3:14dc:7c6d:d7d1:d432:1320:b542:49a3
  IPv6 address for eth0: fde3:14dc:7c6d:d7d1:21a9:469a:834e:f34e

This message is shown once a day. To disable it please create the
/home/phoovich/.hushlogin file.
phoovich@WIN-5JSUBCST74A:/mnt/c/Users/phoovich$
```

> [!NOTE]
> exit wsl using the `exit` command.

This launches your Linux terminal directly inside Windows.

```powershell
phoovich@WIN-5JSUBCST74A:/mnt/c/Users/phoovich$ exit
exit
PS C:\Users\phoovich>
```

---

## **Example: Using `curl` to Connect to a Bitcoin Node**

After installation, you can use common Linux commands such as `curl`.  
For example, to query blockchain information from your Bitcoin node:

```bash
$ curl --user bitcoin --data-binary '{"jsonrpc": "2.0", "id": "curltest", "method": "getblockchaininfo", "params": []}' \
-H 'content-type: application/json' http://<your-ip-bitcoin-node>:8332/
Enter host password for user 'bitcoin':
{"jsonrpc":"2.0","result":{"chain":"main","blocks":918409,"headers":918409,"bestblockhash":"000000000000000000005026984991e19f7504f005bbc6076ac47dc91a3b705a","bits":"1701ddb4","target":"00000000000000000001ddb40000000000000000000000000000000000000000","difficulty":150839487445890.5,"time":1760077332,"mediantime":1760075685,"verificationprogress":0.9999989537224061,"initialblockdownload":false,"chainwork":"0000000000000000000000000000000000000000ea1437872d3e212d0551fe7a","size_on_disk":787936722435,"pruned":false,"warnings":[]},"id":"curltest"}
```
