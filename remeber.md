aliases, TCP wrapper, access :     
samba, sinetd, fsftpd =     
나머지는 tab    

```/etc/httpd.conf
  vi /etc/httpd.conf

  ServerRoot  /www
  DocumentRoot "/usr/local/apache/html"
  UserDir www
  DirectoryIndex index.htm index.html index.php
  Listen 8080                    
  ServerName www.ihd.or.kr:8080
  ServerAdmin ihd@ihd.or.kr
```

```httpd.conf
  vi /etc/httpd/conf/httpd

  <Directory "/www/ihd/admin">
  order Deny, Allow
  Deny from ALL
  Allow from 192.168.22.
  </Direcory>
```

***


```DNS
acl "hang" {192.168.1.12;};
allow-query{192.168.5/24; 192.168.12.22;};

forward only;
forwarder{168.126.63.13;};
```

```
  options {
    directory "/var/named";
    forawrd first;
    forwarders  {8.8.8.8;};
    datasize  1024M;
    allow-query {192.168.64/24;};
  };
```
***

```samba
vi /etc/samba/smb.conf
[www]
comment = web Directory
path = /usr/local/apache/htmldocs
valid users = ihduser kaituser
writable = yes   or write list = ihduser kaituser
```


hosts allow = 192.168.12.0/255.255.255.0
![image](https://user-images.githubusercontent.com/62640332/139590751-3185e530-cefb-4239-a71f-2fe4d63f6642.png)

***

```samba
  smbclient //ihd/ihd_share
```
***
```aliases
vi /etc/aliases

webmaster: ihduser, kaituser
admin :: include: /etc/mail/_admin

new aliases
```
***

```virtusertable
vi /etc/mail/virtusertable

@ihd.or.kr  ihdadmin

ihd.or.kr 도메인으로 들어오는 모든 메일을 ihdadmin 계정으로 전달.
```

***


```
acl all src 0.0.0.0/0.0.0.0 

ACL은 Access Control의 약자로 프록시 서버에 접근할 수 있는 범위를 설정하는 옵션으로    http_access와 함께 사용해야 한다. 

all의 범위는 src옵션으로 지정한 범위는 속하는 네트워크를 지정한다. 
현재처럼 0.0.0.0/0.0.0.0으로 설정하면 모든 네트워크에 대해서 프록시서버에 접근할 수 있다. 

자신의 프록시서버에 제한없이 모든 네트워크들이 접근할 수 있도록 설정한 후 httpd_access로    프록시 서버사용권한을 부여할 수 있다.
```

```
 acl all src 0.0.0.0/0.0.0.0
 http_access allow all
 
 모든 네트워크들이 자신의 프록시서버를 이용할 수 있게 지정한 것이다. 
 이 경우에는 네트워크 트래픽을 초래할 수 있다. 
```
***


```
 acl user src 192.168.3.69            
 acl all src 0.0.0.0/0.0.0.0               
 http_access allow ser            
 http_access deny all                   
 
192.168.3.69 네트워크주소를 user 만 프록시서버 접속을 허용하고, 다른 네트워크에 대해서는 접속을 거부한다.
```


```squid
http_port 8080

acl ihdnet src 192.168.5.0/24
http_access allow ihdnet
```
***

 
```
acl members src 192.168.3.0/255.255.255.0           
acl all src 0.0.0.0/0.0.0.0             
http_access allow members              
http_access deny all                     
 
 192.168.3.0 네트워크주소를 members 범위로 규정하여 http_access 에서 프록시서버 접속을    허용하고, 다른 네트워크에 대해서는 접속을 거부한다.
```

***



```xinetd.conf
 This is the master xinetd configuration file. Settings in the
 default section will be inherited by all service configurations
 unless explicitly overridden in the service configuration. See
xinetd.conf in the man pages for a more detailed explanation of

 defaults        
 {         
  The next two items are intended to be a quick access place to                             
  temporarily enable or disable services.                
               
        enabled         =                    
        disabled        =                             
                
  Define general logging characteristics.             
       log_type        = SYSLOG daemon info                     
       log_on_failure  = HOST                
       log_on_success  = PID HOST DURATION EXIT               
                
  Define access restriction defaults                         
                            
        no_access       =                                    
        only_from       =                   
        max_load        = 0                    
       cps             = 50 10             
       instances       = 50                    
       per_source      = 10                                   
               
  Address and networking defaults            
                

```                        

***
           
cat /etc/xinetd/rsync 파일
```
 [root@localhost xinetd.d]# cat rsync        
 \# default: off                    
 \# description: The rsync server is a good addition to an ftp server, as it                                     
 \# allows crc checksumming etc.                
 service rsync  <<<< telnet 등 서비스 이름               
 {                             
 disable = yes            
 flags = IPv6                                
 socket_type     = stream               
 wait            = no           
 nice            = 5
 acess_times     = 08:00-20:00     
 per_source      = 7
 log_type        = FILE var/log/telnetd.log
 user            = root                        
 server          = /usr/bin/rsync                         
 server_args     = --daemon             
 log_on_failure  += USERID                        
 }              
```
***


```nfs의 exprots
vi /etc/exprots
/nfs_share 192.168.5.0/24(rw, no_root_squash)
```

***


```fstab
/dev/sdb  /data     ext4  defaults,usrquota   0   1

/dev/sdb을 /data 디렉터리에 ext4로 자동 마운트

부팅시 /dev/sb1 디바이스의 파일시스템 점검하고, dump 안함
```

***

```dhcp.conf
ddns-update-sylte nome;
subnet 192.168.100.0 netmask 255.255.255.0 {
  range 192.168.100.101 192.168.100.199;
  option  domain-name-servers ns.linuxmaster.org;
  option  domain-name "linuxmaster.org;
  option router 192.168.100.254;
  default-lease-time 3600;
  max-lease-time 7200;

할당 IP 192.168.100.101 ~ 192.198.100.199
도메인 네임 서버는 ns.linuxmaster.org
도메인 명 linuxmaster.org
게이트웨이 IP 주소는 192.168.100.254
```


***
#### vsftpd

```
  vi etc/vsftpd/vsftpd.conf

  chroot_local_user = YES       접속한 사용자의 홈 디렉터리를 최상위 디렉토리로 지정
  local_umask = 002              파일 생성 적용되는 umask 값
  listen_port = 21               vsftpd 데몬이 외부 접속 요청시 통신할 ftp 포트       
  xferlog_enable  = YES          파일 송수신 로그 지정된 파일에 저장

```



```
  vi /etc/vsftpd/ftpusers

  ihduser
  kaituser

위 두사용자 ftp 서버 접근 거부
```