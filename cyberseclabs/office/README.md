# Solving [CyberSecLabs-office](https://www.cyberseclabs.co.uk/labs/info/Office/)

* [Nmap](https://github.com/nmap/nmap) scan shows port `22`, `80` and `443` are up
* Port `80` has a wordpress site, and Port `443` shows a default apache-server page
* All links on wordpress site redirect to `office.csl`
* `office.csl` looks like a vhost because it doesn't open anything since DNS response comes empty
* Adding local DNS for `office.csl` to machine_ip makes all the links work
* Running [ffuf](https://github.com/ffuf/ffuf) show a directory `forum` exists
* `http://office.csl/forum` shows some chat logs where LFI gives us `.htpasswd` as follows
```
dwight:$apr1$7FARE4DE$lKgF/**************/ (redacted)
```
* [hashID](https://github.com/psypanda/hashID) tells me it's probably a MD5 hash which can be encrypted with mode 1600 of [hashcat](https://github.com/hashcat/hashcat)
* Hashcat decrpyts the password as `c*****` (redacted)
* Logging in with `dwight` and `c*****` at `http://office.csl/wordpress/wp-admin`
* Uploading a PHP reverse-shell from [PayloadsAllForThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
* Current user is `www-data` but can run `/bin/bash` as `dwight` which gave us `access.txt`
* [linPeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) shows the machine has port `10000` open and webmin is running on it seeing the system files but it's not accessible publicly
* To access webmin, we need to do port forwarding so webmin thinks it's dwight trying to log-in, and one of the ways to do that is [SSH Port Forwarding](https://www.ssh.com/ssh/tunneling/example)
* Generating a ssh-key-pair as dwight and forwarding the port with following command on the attacking machine
```bash
ssh -i id_rsa -L 10000:localhost:10000 dwight@machine_ip
```
* Webmin is now accessible on `localhost:10000` and can be exploited using `exploit/linux/http/webmin_backdoor` giving us the `system.txt`
