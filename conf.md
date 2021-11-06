***
### rsyslog, apache, smaba, mail, xinetd, nis, nfs
***

![image](https://user-images.githubusercontent.com/62640332/139581014-597ce0d7-9110-4b20-bb8a-149421eeddb1.png)



***
#### /etc/rsyslog.conf

> 설정파일의 구성: facility.priority; facility.priority;    action


- Facility                         
> ① cron: cron, at과 같은 스케쥴링            
② auth, security: login과 같은 인증                 
③ authpriv: ssh와 같이 인증이 필요한                                  
④ daemon: telnet, ftp 등과 같은 데몬               
⑤ kern: 커널             
⑥ lpr: 프린트              
⑦ mail: 메일                  
⑧ mark: syslogd에 의해 만들어지는 날짜 유형              
⑨ user: 사용자 프로세스                   

- Priority                         
> ① none: 지정한 facility를 제외              
② debug: 프로그램 디버깅                 
③ info: 통계, 기본 정보 메시지           
④ notice: 특별히 주의를 요하나, 에러는 아님           
⑤ warning, warn: 주의가 필요한 경고 메시지            
⑥ error, err: 에러 발생               
⑦ crit: 크게 급하지는 않지만 시스템에 문제가 생김               
⑧ alert: 즉각 조치 필요              
⑨ emerg, panic: 모든 사용자들에게 전달해야 할 위험한 상황                           

- Action                   
>① file: 지정한 파일에 로그 기록            
② @host: 지정한 호스트로 메시지 전달               
③ user: 지정한 사용자가 로그인 한 경우, 해당 사용자의 터미널로 전달                     
④ *: 현재 로그인 되어 있는 모든 사용자의 화면으로 전달        
⑤ 콘솔 또는 터미널: 지정한 터미널로 메시지 전달                          

예시

- 보안 및 승인에 관한 메세지 중에서 모든 경우를 /var/log/secure 에 보낸다.
> authpriv.*    /var/log/secure

- 모든 상황에서 발생하는 메세지 중에서 info 수준(통계 및 기본정보) 이상인 경우를 /var/log/messages에 보낸다. 단, mail 관련 메세지는 제외한다.
> *.info;mail.none    /var/log/messages

- 모든 상황에서 발생하는 메세지 중에서 info 수준(통계 및 기본정보) 인 경우를 /var/log/hihi에 보낸다.
> *.=info    /var/log/hihi                                                                                       
                        
- mail 관련된 메세지 중에서 info 수준의 메세지를 제외하여 /var/log/maillog에 기록한다.
> mail.*;mail.!=info    /var/log/maillog

- mail 관련된 모든 메세지 /var/log/mail에 기록, debug 수준의 로그는 제외
> mail.*;mail.! = debug  /var/log/mail

- uucp 및 news 에서 방생하는 warning 수준이상의 메세지는 /var/log/news에 기록
> uucp,news,warn or uucp,news,warning or uucp.warn; news.warn /var/log/news

- 모든 서비스에 대해 alter 이상의 메세지는 IP 주소가 192.168.12.22인 호스트에 UDP 기반으로 전달한다.
> *.alter @192.168.12.22

**\# 골뱅이 하나면 UDP, 두개면 TCP**



***
#### apache httpd.conf
- ServerType   Standalone
: 서버 타입을 설정하는 지시자 이다.        
> ServerType 에서 설정할 수 있는 것은 Standalone 과 inetd 두가지 설정이 있다.        
            
- ServerRoot  /usr/local/apache                  
: 아파치 서버의 루트 디렉토리를 설정한다.              
           
- PidFile  /usr/local/apache/logs/httpd.pid            
: 아파치가 실행될 때 생성되는 httpd.pid 파일이 생성될 경로를 지정한다.        

- ScoreBoardFile  /usr/local/apache/logs/httpd.scoreboard    
: 부모 프로세스가 자식 프로세스와 의사 소통을 할 때 사용되는 지시자와 그 파일을 지정한다.                        

- Timeout    300                  
: 클라이언트 요청에 의해 서버와 연결이 될 때, 서로간에 아무런 메시지가 발생하지 않을 때  연결을 끊는 시간입니다. 

※ Rest API 경우에는 낮을 수록, 웹서비스에 경우 높을 수록 좋을 것 같습니다.                 

- KeepAlive  On
: 서버와의 지속적인 연결을 하도록 설정되어 있다. 즉 한번의 연결에 대해 한번의 요청만 처리하는 것이 아니라 또 다른 요청을 기다리게 된다. 하지만 지속적인 연결 시간은 KeepAliveTimeout 값에 설정한 만큼 유지된다. KeepAlive를 Off로 설정하게 되면 클라이언트로 부터 한번의 요청을 받은 후 바로 접속을 해제한다.              
\# 특별한 경우가 아니라면 On 상태로 유지하는 것이 좋다.           

- MaxKeepAliveRequests    100                      
: KeepAlive 상태에서 처리할 최대 요청 처리 건수를 설정한다.                
\# 보통의 웹 사이트에서는 설정값 100으로 충분하다.               

- KeepAliveTimeout   15          
: KeepAlive 상태를 유지할 시간을 초 단위로 설정한다.                         

- MinSpareServers   5           
: 아파치가 실행될 때 최소 예비 프로세스 수를 설정한다. 이 값에 의해 현재 nobody 소유의 아파치 프로세스가 5보다 작을 경우 자동으로 부족한 만큼의 아파치 프로세스 생성한다.             
\# 8 정도 설정하는 것이 적당하다.           

- MaxSpareServers  10
: 아파치가 실행될 때 최대 예비 프로세스 수를 설정한다. 이 값에 의해 현재 nobody 소유의 아파치 프로세스가 10보다 클 경우 불필요한 프로세스를 제거한다. 
\# 20 정도 설정하는 것이 적당하다.

- StartServers   5                 
: 아파치가 실행될 때 생성 시키는 자식 프로세스 수이다.  하지만 이 값이 MinSpareServers 값보다 작을 경우 아파치 실행 후에 바로 MinSpareServers 의 설정만큼 생성하기 때문에 아무런 의미가 없게 된다.  
\# StartServer 값과 MinSpareServers 값은 같은 값을 설정하는것이 바람직하다.    

- MaxClients   150                               
: 아파치 서버의 동시 접속자 수를 정의한다.                 
 \# 최대 값은 256이다.  256 이상의 값을 설정하고 싶을 때는 아파치 소스의 httpd.h 헤어 파일의 ARD_SERVER_LIMIT 부분을 수정하고 아파치를 다시 컴파일 해야 된다.             

- MaxRequestsPerChild   0                     
: 아파치의 자식 프로세스가 처리할 수 있는 최대 요청 처리 건수를 설정한다.           
\# 0 은 무제한을 뜻한다.                  

- BindAddress   *                             
: 가상 호스트를 지워한다. 기본적으로 주석 처리 되어 있지만 실제로는 가상 호스트에 영향을 주지 않았다.               
               
- Port      80        
: 아파치가 사용할 기본 포트를 지정한다.                       

- User   nobody                  
- Group  nobody            
: 자식 프로세스가 생성될 때 그 프로세스의 소유자와 소유그룹을 결정한다.                          
\# 보안상 절대 root 로 설정하는 일은 없도록 한다.                 

- ServerAdmin    admin@rootman.co.kr 
: 아파치 서버 관리자 e-mail을 설정하는 부분이다.              

- ServerName    rootman.co.kr           
: 아파치 서버가 작동중인 서버 이름을 설정한다. 기본적으로 주석 처리 되어 있다.             
도메인이 아닌 IP 주소로 사용자의 홈페이지에 접속할 때 URL 끝에 /를 붙여야 접속이 되는 경우가 있는데 이럴 경우# ServerName 지시자에 주석을 제거 하고 아이피 주소를 설정해 주면 된다.

- DocumentRoot    "/usr/local/apache/htdocs"
: 아파치의 웹 문서들의 루트 디렉토리를 지정한다.

ㅁ 예시
```/etc/httpd.conf
  vi /etc/httpd.conf

  ServerRoot  /www
  DocumentRoot "/usr/local/apache/html"
  UserDir www
  DirectoryIndex index.htm index.html index.php
  Listen 8080                     << 아래꺼와 둘중 하나만 해주면 port 설정가능
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
#### DNS 설정

- IN: IN 다음으로 나오는 우측 설정을 사용 한다는 의미이다.
- SOA: start of authority의 약어

[도메인] 탭 [IN] 탭 [A, NS, MX등] 공백or탭 [우선순위 숫자] 탭 [도메인 or IP]

첫번째 항목에 @나 아무것도 입력하지 않으면 네임서버 환경설정 파일에서 설정한 도메인(userdomainname.com)으로 인지한다.

도메인을 직접 적어주려면 마지막에 (닷). 루트 도메인을 입력해 주어야 한다.     
  // 공백 = @ = userdomainname.com.

만약 서브도메인을 입려하고자 한다면 ns, www, mail 또는 전체도메인으로 입력해야 한다.      
 // www = 192.168.20.128 = userdomainname.com.

- S 레코드는 해당 도메인의 네임서버를 지정하는 레코드이다. NS 레코드 다음에 네임서버의 도메인을 입력한다.

- MX 레코드는 Mail Exchanger의 약어로 해당 도메인에 대한 메일서버를 지정하는 레코드이다. 일반적으로 메일서버를 여러개 사용할 때 MX레코드 다음에 우선순위 번호를 지정하여 메일을 수신할 수 있다.  
 
>  IN              MX 10          mail.userdomainname.com.
>  IN              MX 20          mail2.userdomainname.com.

위와 같이 설정한다면 메일 수신시 우선순위가 빠른 10번의 mail.userdomainname.com.에서 메일을 수신하는데 만약 10번의 메일서버에 장애가 발생하면 20번의 mail2.userdomainname.com. 메일서버로 메일을 수신하도록 한다는 의미임.
 

- A 레코드는 해당 도메인의 실제 IP주소를 설정하는 레코드이다.
>  IN              A           211.108.52.60

 

위와 같이 설정한다면 userdomainname.com.도메인의 아이피가 211.108.52.60번이라고 정의하는 설정임

서브 도메인을 설정하려면 가장 앞부분에 서브도메인명을 입력하고 A 레코드로 IP 번호를 지정하면 된다.

 

>  ns           IN            A           211.108.52.60
>  www        IN            A           211.108.52.60

 

- CNAME 레코드는 별칭을 설정하여 사용하는 레코드이다.    
- CNAME 레코드를 사용하고 A 레코드로 정의한 도메인을 적어주면 A 레코드의 도메인 IP를 별칭으로 사용함으로써 mail도메인에 대해서는 www의 IP를 사용하도록 지정하는 방법이다.

>  mail          IN         CNAME            www
>  ftp           IN         CNAME            @


위처럼 도메인 IP번호를 별칭을 이용하여 @로 지정하면 /etc/named.rfc1912zones파일에서 정의한 ORIGIN 도메인명을 받아와서 IP를 설정하기 때문에 211.108.52.60을 의미하게 된다.




ㅁ 레코드 종류

|타입|의미|
|---|---|
|NS|도메인 메임서버 정보|
|MX|도메인의 MX(Mail Exchanger)서버|
|A|호스트의 IP주소(Address)|
|CNAME|별칭으로 부여된 canonical name|
|SOA|도메인의 start-of-authority정보|
|HINFO|호스트의 CPU 정보와 운영체제 정보|
|MINFO|메일박스와 메일 리스트 정보|
|PTR| IP주소에 대한 호스트명|
|TXT| 호스트에 대한 텍스트 정보|
|UINFO| 사용자 정보|
|ANY|호스트에 관련된 모든 레코드 정보|


```DNS
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


<br>
<br>
<br>

- Zone 설정
: 도메인 -> IP 바꾸어주는것

```
 @        IN    SOA     ns.ihd.or.kr.   kait.ihd.or.kr.  (
 
                     20010504            ; serial
 
                     10800               ; refresh
 
                     3600                ; retry
 
                     3600000             ; expire
 
                     43200  )            ; mininum
 
           IN   NS        ns.ihd.or.kr.
 
           IN    A          192.168.5.13
 
           IN    MX 10     ihd.or.kr.
 
           www   IN    A    192.168.123.254
 
           www1    IN   CNAME    www
```


도메인은 ihd.or.kr 이고, 관리자 메일은 kait@ihd.or.kr
ihd.or.kr 도메인으로 메일을 받을수 있도록 설정
www 도메인을 사용하는 호스트의 IP 주소를 192.168.5.13


- Reverse Zone 설정
: IP --> 도메인 바꾸어주는것
```
@        IN    SOA      ns.ihd.or.kr.   kait.ihd.or.kr.  (

                    20010504            ; serial

                    10800               ; refresh

                    3600                ; retry

                    3600000             ; expire

                    43200  )            ; mininum

  IN  NS  ns.ihd.or.kr.
(15    IN)  PTR ihd.or.kr.   << 15 IN 은 생략 가능
```

IP 주소는 10.0.2.15 이고 설정하는 도메인 ihd.or.kr  

관리자 메일 주소는 kait@ihd.or.kr로 설정한다.     

네임 서버는 ns.ihd.or.kr을 사용

10.0.2.15 인 IP 주소를 조회하면 ihd.or.kr가 나타나도록 설정.

***
#### 삼바

ㅁ smb(파일 공유 삼바 서비스)

 1) 경로 : /etc/init.d/smb

 2) 요약 : 윈도우와 공유 디렉터리 설정을 위한 데몬

 3) 추가 설명

리눅스와 유닉스에서 삼바를 이용하면 공유 디렉터리나 프린터 등의 자원을 윈도우 시스템과 공유하여 사용할 수 있습니다. 삼바 서버의 설정은 /etc/samba/smb.conf 파일에서 할 수 있습니다.


![image](https://user-images.githubusercontent.com/62640332/139590751-3185e530-cefb-4239-a71f-2fe4d63f6642.png)

ㅇ global 설정 지시어

|지시어|	설명|
|---|---|
|workgroup|	서버가 속할 네트워크 그룹을 지정|
|server string	|서버가 네트워크에서 구분될 이름을 지정|
|hosts allow	|서버에 접근을 허용할 클라이언트 대역폭을 지정|
|printcap name|	printcap 이름을 지정|
|load printers|	프린터 시스템을 지원|
|guest account|	게스트 사용자의 접근을 허용|
|log file|	저장할 로그 파일을 지정|
|max log size|	로그 파일 크기를 제한|
|security	|user, share, server의 시큐리티 등급을 지정|
|encrypt passwords|	패스워드를 암호화|
|smb passwd file	|삼바 패스워드 파일의 위치를 지정|
|socket options	|지정된  옵션을 사용. 설정된 소켓 옵션이 최적의 성능 향상을 가져옴|
 

 ㅇ homes
: 사용자 홈 디렉터리에 대한 공유 설정입니다. 위 파일에 설정 기본값을 사용하면 됩니다.

 

 ㅇ printers
: CUPS 프린트 시스템이라면 각 프린트의 정의를 새로 설정해 줄 필요가 없습니다. 다만 public = yes라고 지정하면 게스트 사용자(여기서는 pcguest)도 프린트를 사용할 수 있습니다.

 

 ㅇ public
: 위 설정에서는 /home/samba 디렉터리를 윈도우와 공용으로 사용하는 디렉터리로 지정했습니다.

 

 ㅇ movie
: 위 설정에서 /home/movie 디렉터리를 chan 사용자에게만 접근할 수 있게 했습니다. 모든 설정을 완료한 후 삼바 서비스 데몬을 실행합니다.

```samba
vi /etc/samba/smb.conf
[www]
comment = web Directory
path = /usr/local/apache/htmldocs
valid users = ihduser kaituser
writable = yes   or write list = ihduser kaituser
```


***
ㅁ smbclient
1) 경로 : /usr/bin/smbclient

 2) 요약 : SMB/CIFS 서비스에 접근하기 위한 클라이언트

 3) 사용 방법

   sbmclient [-b <buffer size>] [-d debuglevel] [-e] [-L <netbios name>] [-U username] [-I(아이) destinationIP] [-M <netbios name>] [-m maxprotocol] [-A authfile] [-N] [-g] [-i scope] [-O <socket options>] [-p port] [-R <name resolve order>] [-s <smb config file>] [-l] [-P] [-c <command>]

   smbclient {servicename} [password] [-b <buffer size>] [-d debuglevel] [-e] [-D Directory] [-U username] [-W workgroup] [-M <netbios name>] [-m maxprotocol] [-A authfile] [-N] [-g] [-l log-basename] [-I(아이) destinationIP] [-E] [-c <command string>] [-i scope] [-O <socket options>] [-p port] [-R <name resolve order>] [-s <smb config file>] [-T<c|x>IXFqgbNan] [-k]

 
|옵션|내용|
|---|---|
|-R, --name-resolve=NAME-RESOLVE-ORDER | 네임 서버(NAME-RESOLVE-ORDER)를 지정|
|-M, --message=HOST | 메시지를 보냄|
|-I(아이), --ip-address=IP | IP를 지정|
|-E, --stderr | 표준 출력으로 메시지를 출력|
|-L, --list=HOST | 공유 리스트 목록(HOST)을 지정|
|-t, --terminal=CODE | 터미널 I/O 코드(CODE)를 지정. \{sjis \| euc \| jis7 \| jis8 \| junet \| hex\}가 올수 있음|
|-T, --tar=<c|x>XFqgbNan | 명령행의 tar를 지정|
|-D, --directory=DIR | 디렉터리(DIR)를 지정|
|-c, --command=STRING | 명령어(STRING)를 지정. 명령어 간에 세미콘론(;)으로 구분|
|-b, --send-buffer=BYTES | 전송 버퍼의 크기(BYTES)를 지정|
|-p, --port=PORT | 포트(PORT)를 지정|
|-g, --grepable | grep 사용 가능한 목록으로 출력|
|-B, --browse | DNS 를 이용하여 SMB 서버를 브라우징함|
|-?, --help | 사용법을 출력|
|--usage | 간단한 사용법을 출력

<br>
<br>
<br>

|공통 삼바 옵션| 내용|
|---|---|
|-d, --debuglevel=DEBUGLEVEL | 디버깅 레벨(DEBUGLEVEL)을 지정|
|-s, --configfile=CONFIGFILE | 설정파일(CONFIGFILE)을 지정|
|-l, --log-basename=LOGFILEBASE | 로그 파일의 이름(LOGFILEBASE)을 지정|
|-V, --version | 버전 정보 출력|

 

|접속 옵션|내용|
|---|---|
|-O, --socket-options=SOCKETOPTIONS | 소켓 옵션(SOCKETOPTIONS)을 지정|
|-n, --netbiosname=NETBIOSNAME | 넷 바이오스 이름(NETBIOSNAME)을 지정|
|-W, --workgroup=WORKGROUP | 워크 그룹(WORKGROUP)을 지정|
|-i, --scope=SCOPE | 넷 바이오스의 범위(SCOPE)를 지정|

 

|인증 옵션|내용|
|---|---|
|-U, --user=USERNAME | 로그인 사용자(USERNAME)를 지정|
|-N, --no-pass | 패스워드를 묻지 않음|
|-k, --kerberos | 커버로스 인증을 이용|
|-A, --authentication-file=FILE | 인증 파일(FILE)을 지정|
|-S, --signing=on|off|required | on, off, required 중에서 클라이언트 사인을 지정|
|-P, --machine-pass | 저장된 계정 패스워드를 사용|
|-e, -encrypt | SMB 전송을 암호화(유닉스 계열 서버만)|


```samba
  smbclient //ihd/ihd_share
```


***
#### /etc/ssh/sshd_config

<br>

ssh가 사용할 기본 포트 지정        
> Port 22                            

​- 로그인 허락할 계정을 기록한다.
> Allowusers user1 root     ----  user1과 root 두 계정에게만 로그인 허용

- openssh는 프로토콜 버전을 원하는 대로 선택할 수 있다.

  protocol 2로 설정에는서버는 버전 2로만 작동하기 때문에

  ssh1을 사용해 접속을 요청하는 클라이언트를 받아 들일 수 없다.

- protocol 1로 설정해서 가동시킬 경우에는 버전 2를 사용하는 ssh2 사용자의 요청을
 ​받아 들일 수 없다.  보안상 protocol 1 은 사용하지 않는다.
> Protocol 2                         
 
​- sshd 데몬이 통신가능한 주소이다. 0.0.0.0은 모든 네트워크
> ListenAddress 0.0.0.0        
 


- protocol 1.3과 1.5에 의해 사용되어지는 private RSA 호스트 키 값이 저장되어 있는 파일이다.     
현재는 /etc/ssh/ssh_host_key에 저장되어 있다.    
pulick key(공개키)는 /etc/ssh/ssh_host_key.pub이다.    

> HostKey for protocol version 1
> HostKey /etc/ssh/ssh_host_key        => protocol version 1은 사용하지 않는다.



 - 서버의 키는 한번 접속이 이루어진 뒤에 자동적으로 다시 만들어진다.
   다시 만드는 목적은 나중에 호스트의 세션에 있는 키를 캡처해서  암호를 해독하거나 훔친 키를 사용하지 못하도록 하기 위함 위함이다.   
   ​값이 0이면 키는 다시 만들어지지 않는다. 기본값은 3600초이다.    
  이값은 자동으로 키를 재생성하기 전까지 서버가 대기할 시간을 초단위로 정의한다.   

> KeyRegenerationInterval 3600                => protocol version 1은 사용하지 않는다.
 

- 서버 키에서 어느정도의 비트수를 사용할지 정의한다.

- ​최소값은 512이고 디폴트 값은 768이다. (\# 2048 권장)
> ServerKeyBits 1024                        => protocol version 1은 사용하지 않는다. 
 

-  유저의 로그인이 성공적으로 이루어지 않았을 때 이 시간 후에 서버가​ 연결을 끊는 시간이다.
- 값이 0이면 제한    시간이 없다. 기본값은 600초이다.

 > Authentication:
LoginGraceTime 600


- root 로그인 허용여부를 결정하는 것이다.    
- yes, no, without-password를 사용할 수 있다.    
현재 no로 되어 있기 때문에 직접 root로 접속이 불가능하다.
 이옵션을 yes 로 하기보다는 일반계정으로 로그인후 su 명령으로 root로 전환하는 것이 보안상 안전하다.           

> PermitRootLogin no   ==> 보안상 no로 해준다. 
 

- 패스워드 인증을 허용한다. 이 옵션은 프로토콜 버전 1과 2 모두 적용된다.   
인증할 때 암호기반 인증방법의 사용 여부를 결정한다.    
강력한 보안을 위해 이옵션은 항상 ‘no‘로 설정해야한다.   

> PasswordAuthentication yes         
 

- 패스워드 인증을 할 때 서버가 비어있는 패스워드를 인정하는 것이다.
  ​기본 값은 no이다.

  > PermitEmptyPasswords no


  ***
  #### 메일관련 설정 파일

- /etc/mail/local-host-names
: 메일 서버에 사용되는 도메인을 입력한다. 한줄에 한 도메인이 입력되는 간단한 형태

- /etc/mail/sendmail.mc
: sendmail의 매크로 설정 파일. sendmail.cf파일을 복원할 때 사용된다.

- /etc/mail/access
: 메일서버로 접근하는 호스트나 도메인을 제어한다. 

From:abc@test.com  REJECT

From 자리에는 3가지가 들어간다.

> - Connect : 접속하는 클라이언트의 주소, 이름 기반 정책
> - From : 보낸 사람 정책
> - To : 보낼 사라는 정책

REJECT자리에는 4가지가 들어간다.
> - RELAY : 접근 허용
> - REJECT : 메일 거절, 거부 메세지
> - DISCARD : 메일 거절, 메세지 없음
> - OK : DNS가 조회되지 않더라도 메일 허가. 보통 거부된 도메인의 예외 처리시에 사용

- /etc/aliases
: 특정 계정으로 들어오는 메일을 다른 계정으로 전송되도록 설정한다.

- /etc/mail/virtusertable
: 하나의 메일 서버에 여러 도메인을 사용하는 환경에서 동일한 계정을 사용할 때 각각의 도메인으로 연결시킨다.

```virtusertable
vi /etc/mail/virtusertable
@ihd.or.kr  ihdadmin

ihd.or.kr 도메인으로 들어오는 모든 메일을 ihdadmin 계정으로 전달.
```

- ~/.forward
: 각 사용자가 자신에게 들어오는 메일을 다른 메일로 포워딩 할 때 쓴다.

***
#### 프록시(proxy) 서버
:서버와 클라이언트 사이에서 중계기로서 대리로 통신을 수행하는 기능
squid.conf

ㅁ 포트 설정
> http_port 3128   
> 스퀴드 서버 포트를 설정한다. \# - default 값은 3128 이다

ㅁ 캐시 설정
> cache_mem 8 MB   
> 스퀴드 서버에서 사용하는 캐시 사이즈를 설정한다.

> maximum_object_size 4096 KB   
> 캐시 서버에 저장될 수 있는 객체 즉, 파일의 크기를 제한하는 옵션이다.

> cache_dir ufs /usr/local/squid/cache 100 16 256 
> 
> 캐시가 저장될 경로를 지정해주는 항목으로 크기와 생성될 하위 1차 및 2차 디렉토리의 수를 지정한다.    
> 현재 설정은 /usr/local/squid/cache 디렉토리에 캐시데이터들이 최대 100MB 까지 저장될 수 있고, 캐시가 저장될 1차 디렉토리는 16 개로 설정하고 그 밑에 2차 디렉토리 수를 256 개로 > 설정한다.   

> cache_mgr admin ----- 캐시서버의 관리자 계정을 지정한다.

> cache_effective_user squid
> cache_effective_group squid
>
>스퀴드 서버 캐쉬 디렉토리의 소유자와 소유자그룹을 나타낸다.
>현재 설정은 cache 가 squid 란 uid/gid 로 작동하도록 설정한다.


ㅁ 로그 설정
> cache_access_log /usr/local/squid/logs/access.log ----- 접근로그를 기록하는 파일을 설정한다.

> cache_log /usr/local/squid/logs/cache.log ----- 캐쉬설정에 관한 로그를 기록하는 파일을 설정한다.


> cache_store_log /usr/local/squid/logs/store.log ------ 저장되는 로그(이미지, 아이콘 등)를 기록하는 파일을 설정한다.

> debug_options ALL,1
> 
> 스퀴드가 동작할 때 오류체크 기능을 사용하여 로그파일에 기록할 수 있게 하는 옵션이다.    
> 현재 설정은 모든 항목에 대해 기본적인 값만 로그에 남도록 설정한 것이다.    


> buffered_logs on ------ 로그 기록시 사용되는 시스템 자원을 절약함으로써 약간의 속도 향상을 기대할 수 있는 옵션이다.


ㅁ 특정사이트 차단
> acl blocksitelist dstdomain "blockwebsites.lst"
> http_access deny blocksitelist


> cat blockwebsites.lst
> www.naver.com
> www.jejunu.ac.kr

ㅁ 접근 설정
> visible_hostname 호스트이름 ----- squid 서버에 특정 호스트 이름을 부여할수 있다.

```
acl all src 0.0.0.0/0.0.0.0 

ACL은 Access Control의 약자로 프록시 서버에 접근할 수 있는 범위를 설정하는 옵션으로 >http_access와 함께 사용해야 한다. 

all의 범위는 src옵션으로 지정한 범위는 속하는 네트워크를 지정한다. 
현재처럼 0.0.0.0/0.0.0.0으로 설정하면 모든 네트워크에 대해서 프록시서버에 접근할 수 있다. 

자신의 프록시서버에 제한없이 모든 네트워크들이 접근할 수 있도록 설정한 후 httpd_access로 >프록시 서버사용권한을 부여할 수 있다.
```

```
 acl all src 0.0.0.0/0.0.0.0
 http_access allow all
 
 모든 네트워크들이 자신의 프록시서버를 이용할 수 있게 지정한 것이다. 
 이 경우에는 네트워크 트래픽을 초래할 수 있다. 
```


```
http_access deny all

클라이언트가 프록시 서버에 접속을 허용할 것인지 거부할 것인지 결정해주는 옵션으로 acl과 함께 사용된다. 
http_access다음에 all 또는 deny를 지정하고 acl리스트 중 하나를 지정해 사용한다.
```

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


<br>           
```
acl members src 192.168.3.0/255.255.255.0           
acl all src 0.0.0.0/0.0.0.0             
http_access allow members              
http_access deny all                     
 
 192.168.3.0 네트워크주소를 members 범위로 규정하여 http_access 에서 프록시서버 접속을 >허용하고, 다른 네트워크에 대해서는 접속을 거부한다.
```

***
#### xinetd 설정 파일
- 네트워크 서버/클라이언트 구조에서 다양한 서비스( FTP, Telnet, SSH 등등 ) 서버 프로세스(데몬) 동작 방식은 공통점이 있습니다.

실제 서비스를 제공하는 서비스 프로세스를 제외하고는 클라이언트의 접속 요청이 있을 때까지 대기하다가 요청이 들어오면 해당 요청을 처리할 서비스 프로세스(자식 프로세스)를 실행하는 형태입니다.

데몬을  관리하는 데몬입니다. 이것을 슈퍼 데몬이라고 부릅니다.
inted의 후속 버전입니다. 
    
            
xinetd 설정 파일의 위치는 /etc/xinetd.conf입니다.            
            
xinetd.conf 파일에는 default 설정 값이 포함되어있습니다.

서비스별로 개별적인 설정이 필요한 경우 /etc/xinetd.d/ 밑에 서비스 별 설정 파일을 수정할 수 있습니다.

 
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
            
3-1  /etc/xinetd.conf 파일
 
- 로그파일을 rsyslog 등 시스템 로그에서 관리되도록 위임하거나, 별도의 파일로 선택할 수 있습니다.
> log_type = SYSLOG | FILE  경로명

- 접속에 실패했을 때 기록될 값을 정합니다.           
 > log_on_failure = HOST | USERID | ATTEMPT


 
- 접속에 성공했을 때 기록될 값입니다.
> log_on_success = PID | HOST | USERID | EXIT | DURATION | TRAFFIC

-  초당 요청수만큼을 초과할 경우 제한시간에 설정된 초만큼 접속을 중단시킵니다.            
> cps = [초당 요청수] [제한시간]

            
- 동시에 서비스할 수 있는 서버의 최대 서버 수를 지정합니다.            
> instances = 최대 서버수


- 같은 IP에서 접속할 수 있는 최대 서비스 수입니다.
> per_source = 최대 접속수


 
- no_access = 접속 불가능한 호스트

- only_form = 접속 가능한 호스트


- only_form과 no_access가 중복되면 차단됩니다.
> enabled = 사용 가능한 서비스 목록
> disabled = 사용 불가능한 서비스 목록

\# enabled과 disabled가 중복되면 차단됩니다.

 

3-2  /etc/xinetd.d 
            
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
 

디렉터리 하위에 서비스 명으로 된 설정 파일을 만들어 서비스(ssh, ftp 등등) 별로 설정할 수 있습니다. 
서비스명은 /etc/서비스 이름과 동일하게 맞추고. conf는 안 붙입니다. 

 
1. socket_type : stream, dgram, raw값을 지정할 수 있습니다.

2. user : 서비스를 사용할 사용자의 이름입니다.
 
3. wait : yes는 단일 스레드, no는 다중 스레드로 동작합니다.

4. server : 서비스가 연결되었을 때 실행할 프로그램입니다.

5. nice : 서버 우선순위
 
이 외에도 xinetd.conf 상의 설정값들은 대부분 이용 가능하다. access_time, redirect, port, nice 등의 설정도 가능합니다.

          
***
#### NIS( Network Information System) 와 NFS(Netowrk File System)
NFS는 사용자들의 home디렉토리들이 어느 시스템에서나 동일하게 보여지도록 하며, 
NIS는 passwd나 group등의 네트워크 정보 파일들을 하나의 서버에서 관리하도록 하여 나머지 시스템에서 
서버에서 제공하는 새로운 정보를 받을 수 있도록 하는 것이다. 

ㅁ NIS 관련 폴더     
ypbind : 클라이언트 프로그램             
yp-tools : NIS 명령 패키지           
ypserv : 서버 프로그램               
            
ㅁ NIS 관련 명령어
            
(1) nisdomainname                

1) 설명: NIS 도메인이름을 보여주거나 지정하는 명령이다.         
2) 사용법                 
> nisdomainname [NIS 도메인이름]              
3) 사용예                
> [root@linux245 root]# nisdomainname                    
test.co.kr              
=> 현재 설정된 NIS 도메인이름을 보여준다.               

> [root@linux245 root]# nisdomainname test.co.kr                  
=> NIS 도메인을 test.co.kr로 지정한다.             
         
(2) ypwhich                   
1) 설명: NIS 클라이언트에서 사용하는 명령어로 NIS 서버의 이름과 관련 맵파일을 보여준다.                             
2) 사용법                   
> ypwhich [option]     
   
3) option              
-m : NIS 서버관련 맵파일을 보여준다.             
  
4) 사용예              
> [root@linux246 root]# ypwhich          
nis.test.co.kr          
=> NIS서버의 보여준다.           
                         
> [root@linux246 root]# ypwhich -m              
=> NIS 서버관련 맵파일을 보여준다.              
                                
(3) ypcat                 
1) 설명: NIS 클라이언트에서 사용하는 명령어로 NIS 서버의 데이타베이스라고 할 수 있는 맵파일의 내용을 확인하는 명령이다.         
2) 사용법        
> ypcat 맵파일        
=> NIS 서버의 맵파일 정보를 보여준다. 맵파일의 확인은 'ypwhich -m' 명령을 이용한다.         
3) 사용법          
> [root@linux246 root]# ypcat hosts.byname 
=> 호스트관련정보를 보여준다.   
            
> [root@linux246 root]# ypcat passwd.byname     
=> NIS 서버의 사용자관련 정보를 보여준다.

(4) yppasswd
1) 설명: NIS 클라이언트에서 NIS 서버에 등록된 사용자의 패스워드를 변경하는 명령이다. 물론 변경하려면 NIS 서버의 root 패스워드도 알아야 한다.            

2) 사용법            
> yppasswd 사용자계정       
   
3) 사용예         
> ypasswd testuser      

(5) ypchsh         

1) 설명: NIS 클라이언트에서 NIS 서버에 등록된 사용자의 쉘을 변경하는 명령이다.            

2) 사용법         
> ypchsh 사용자계정       
   
3) 사용예          
> ypchsh testuser
            
(6) ypchfn              
   
1) 설명: NIS 클라이언트에서 NIS 서버에 등록된 사용자의 정보를 변경하는 명령이다.               

2) 사용법
> ypchfn 사용자계정        
   
3) 사용예          
> ypchfn testuser


```nfs의 exprots
vi /etc/exprots
/nfs_share 192.168.5.0/24(rw, no_root_squash)
```
            
***
#### 디바이스 장치 추가 fstab

```fstab
/dev/sdb  /data     ext4  defaults,usrquota   0   1

/dev/sdb을 /data 디렉터리에 ext4로 자동 마운트

부팅시 /dev/sb1 디바이스의 파일시스템 점검하고, dump 안함
```


***
#### dhcpd.conf

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
  vi  vistpd.conf

  local_umask = 002              파일 생성 적용되는 umask 값
  listen_port = 21               vsftpd 데몬이 외부 접속 요청시 통신할 ftp 포트       
  xferlog_enable  = YES          파일 송수신 로그 지정된 파일에 저장

```