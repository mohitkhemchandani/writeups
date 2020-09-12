# FFUF Report

  Command line : `ffuf -u https://office.csl/FUZZ -w /home/nwm/opt/wordlists/seclists/Discovery/Web-Content/common.txt -t 50 -of md -o ffuf -s`
  Time: 2020-09-11T13:01:14&#43;05:30

  | FUZZ | URL | Redirectlocation | Position | Status Code | Content Length | Content Words | Content Lines | ResultFile |
  | :- | :-- | :--------------- | :---- | :------- | :---------- | :------------- | :------------ | :--------- |
  | .htaccess | https://office.csl/.htaccess |  | 11 | 403 | 276 | 20 | 10 |  |
  | .htpasswd | https://office.csl/.htpasswd |  | 12 | 403 | 276 | 20 | 10 |  |
  | .hta | https://office.csl/.hta |  | 10 | 403 | 276 | 20 | 10 |  |
  | forum | https://office.csl/forum | https://office.csl/forum/ | 1825 | 301 | 310 | 20 | 10 |  |
  | index.html | https://office.csl/index.html |  | 2156 | 200 | 10918 | 3499 | 376 |  |
  | server-status | https://office.csl/server-status |  | 3660 | 403 | 276 | 20 | 10 |  |
  | wordpress | https://office.csl/wordpress | https://office.csl/wordpress/ | 4499 | 301 | 314 | 20 | 10 |  |
  