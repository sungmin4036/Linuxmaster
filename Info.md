#### RAID 설정: mdadm

- RAID 어레이 상태를 확인하기
> [root@localhost ~]# more /proc/mdstat
Personalities : [raid1]
md0 : active raid1 sda[0] sdb[0]
40692224 blocks 256k chunks

- RAID 설정 파일 생성 하기
> [root@AP1 /]# more /etc/mdadm.conf 
DEVICE /dev/sdb /dev/sdc
ARRAY /dev/md0 devices=/dev/sdb,/dev/sdc

- RAID 구성 명령어 실행
> [root@AP1 etc]# mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc

- (RAID 포멧하기)
> [root@AP1 proc]# mkfs.ext4 /dev/md0

***
#### useradd 옵션
|옵션|의미|예시|
|---|---|---|
|-p|사용자의 암호를 추가 시에 지정할 수 있다. 현재 리눅스에서는 암호화된 값을 사용하므로 암호화된 값으로 지정해야 한다.|
|-d|홈 디렉터리를 지정할 때 쓰인다. 최종 디렉터리만 생성하므로 중간 경로가 있는 경우에는 미리 생성해야 한다.|useradd -d /home/testUser/ user2|
|-g|그룹을 지정할 때 사용하는데, 지정할 그룹이 미리 생성되어 있어야 한다. 이 옵션을 지정하지 않으면, 레드햇 계열에서는 아이디와 동일한 그룹에 포함시키고, 다른 배포판에서는 users에 포함시킨다.|useradd -g 1002 user5|
|-G|기본 그룹 이외에 추가로 그룹에 속하게 할 경우에 쓴다.|useradd -g 1002 -G 1001 user6|
|-c|사용자 생성 시 사용자에 대한 설명을 설정한다.|useradd -c 'Just for test!!' user8|
|-s|사용자 생성 시 사용자가 사용할 셸을 지정한다.|useradd -s /bin/rbash user11|
|-D|/etc/default/useradd에 설정된 유저 추가와 관련된 기본 사항들을 보여준다.|
|-m|사용자를 생성할 때 홈 디렉터리를 생성해 주는 옵션으로 레드햇 리눅스는 이 옵션을 사용하지 않아도 홈 디렉터리가 생성되지만 다른 배포판에서는 생성되지 않으므로 이 옵션을 지정해야 한다. 보통 -k 옵션과 같이 사용하여 사용자 생성 시 기본적으로 부여되는 목록이 들어있는 skeldir을 지정할 때 사용한다.|
|-k|사용자 생성 시에 제공되는 환경 파일들은 기본적으로 /etc/skel로 지정되어 있는데, 이외의 디렉터리를 지정할 때 쓰인다.|
|-f|패스워드의 만기일을 날짜 수로 지정한다.|useradd -f 3 user10|
|-e|계정의 만기일을 YYYY-MM-DD 형식으로 지정한다.|useradd -e '2020-12-31' user9|
|-u|사용자 추가 시에 UID 값을 지정한다.|useradd -u 1004 user7|

***

#### 파일 시스템 검사

##### - fsck/e2fsck
\# fsck/e2fsck 명령어는 마운트된 드라이브에서는 사용 금지

|옵션|내용|
|---|---|
|-a| 명령 수행에 대한 확인 질문 없이 무조건 수행한다. (권장하지 않음)|
|-A| etc/fstab에 정의되어 있는 모든 파일 시스템을 체크|
|-P|-A옵션을 사용할 때, 루트 파일시스템을 다른 파일시스템과 병렬로 함께 체크한다|
|-r|명령 수행에 대한 확인 질문을 한다.|
|-R|-A옵션을 사용할 때, 루트 파일시스템은 체크하지 않고 건너 뛴다
|-N|실행을 하지 않고 단지 어떤 것이 실행되어지는 지 보여준다.|
|-t fstype|점검할 파일시스템의 유형을 지정한다. 파일시스템 앞에 no를 붙이면 지정한 파일   시스템을 제외한 나머지를 검사하고, -A옵션이 설정돼 있는 경우 /etc/fstab에서   파일시스템 유형이 맞는 것만 검사한다|
|-T|검사를 시작할 때 제목을 보여주지 않는다.|
|-v|버전 정보를 보여준다. |
|-V| 실행되는 각 파일시스템용 명령을 포함해 자세한 출력을 수행한다. |
|-y|모든 응답을 다 yes로 해서 자동으로 실행|
|-n|모든 질문에 대한 응답을 no로 취급|
|-f| 파일 시스템이 이상 유무에 상관 없이 강제적으로 파일 시스템을 체크 |

> fsck.ext4 -y /dev/sdb1
> fsck -a /dev/sdb1

##### - XFS
XFS 파일 시스템 점검
> xfs_repair -n /dev/sda2

***
#### 패키지 관리
##### -yum
* yum check-update : 현재 인스톨된 프로그램 중에 업데이트 된 것을 체크해줍니다.
* yum clean all : 캐시 되어 있는 것을 모두 지웁니다.
* yum deplist : yum 패키지에 대한 의존성 테스트합니다.
* yum downgrade 패키지 : yum을 통한 패키지 다운그레이드합니다.
* yum erase 패키지 : yum을 통한 시스템에서 삭제합니다.
* yum groupinfo 그룹 : 그룹패키지의 정보를 보여줍니다.
* yum groupinstall 그룹 : 그룹패키지를 설치합니다.
* yum grouplist 그룹 : 그룹리스트에 관한 정보를 확인합니다.
* yum groupremove 그룹 : 그룹리스트에 관해 삭제합니다.
* yum help : yum의 도움말을 확인합니다.
* yum info 그룹 또는 패키지 : 패키지 또는 그룹의 패키지를 자세하게 확인합니다.
* yum install 패키지 : 시스템으로 패키지의 Install 을 실시합니다.
* yum list : 서버에 있는 그룹 및 패키지의 리스트를 보여줍니다.
* yum localinstall 패키지 : 로컬에 설치합니다.
* yum makecache : 캐쉬를 다시 올립니다.
* yum provides FilePath명 : 파일이 제공하는 패키지 정보 출력합니다.
* yum reinstall 패키지 : 패키지를 재인스톨 합니다.
* yum update 패키지 : 패키지를 업데이트합니다.
* yum upgrade 패키지 : 패키지를 업그레이드 합니다.
* yum search 키워드 : 키워드로 시작하는 패키지를 검색할수 있습니다.

-- 옵션
* -h, --help : 해당 명령어의 도움말을 보여주고 실행이 종료됩니다.
* -t, --tolerant : 에러를 자동으로 잡아서 설치합니다.
* -C, --cacheonly : 캐시를 업데이트 하지 않고 전체 시스템 캐시 실행합니다.
* -c [config file], --config=[config file] : 파일 위치를 알려줍니다.
* -R [minutes], --randomwait=[minutes] : 최대치의 명령어 실행시 기다립니다.
* -d [debug level], --debuglevel=[debug level] : 최종 결과를 디버깅합니다.
* --showduplicates : 중복요소를 보여줍니다.
* -e [error level], --errorlevel=[error level] : 결과 중 에러를 보여줍니다.
* --rpmverbosity=[debug level name] : rpm에서 결과물을 디버깅합니다.
* --version : Yum 버전을 보여주고 실행이 종료됩니다.
* -y, --assumeyes : 모든 물음에 예를 진행합니다.
* -q, --quiet : 모든 작업이 종료됩니다.
* -v, --verbose : 작업을 장황하게 합니다.
* --installroot=[path] : root권한으로 path위치에 인스톨을 진행합니다.
* --enablerepo=[repo] : 1개 이상의 저장소 위치에 저장시킵니다.
* --disablerepo=[repo] : 1개 이상의 저장소 위치에 저장시키지 않습니다.
* -x [package], --exclude=[package] : 패키지 이름을 제외시킵니다.
* --disableexcludes=[repo] : 이름으로 플러그인을 설치를 중단합니다.
* --obsoletes : 오래된 패키지는 업데이트를 하는 동안 적절히 삭제 및 교체됩니다.
* --noplugins : yum plugin이 없도록 합니다.
* --nogpgcheck : gpg signature를 불가능하게 합니다.
* --skip-broken : 문제 있는 패키지는 자동으로 스킵해서 넘어갑니다.
* --color=COLOR : 컬러가 사용되었을 때 조정합니다.
* --releasever=RELEASEVER : $releasever의 값을 yum config와 repo파일에서 조정합니다.
* --setopt=SETOPTS : 임의로 config와 repo 옵션값을 지정합니다.
* --disablepresto : Presto 플러그인을 중단하고 deltarpm을 다운로드 받지 않습니다.

##### RPM
rpm파일 이름 구조
> centos-release-6-4.el6.centos.10.x86_64.rpm

> + 패키지 이름 : centos-release
> + 패키지 버전 : 6
> + 릴리즈번호 : 4
> + 배포버전 : el6
> + os : centos
> + 아키텍쳐 : x86_64  (noarch : 모든CPU)

rpm <옵션> <패키지>
 + rpm -I : rpm 설치
 + rpm -U : 이미 설치된 패키지 업데이트 혹은 없으면 설치
 + rpm -v : 설치 정보 출력(verbose 모드)
 + rpm -e : 패키지 삭제(의존성 무시 : --nodeps)
 + rpm -qa : 모든 패키지에 대하여 질의
 + rpm -qi : 패키지 이름, 버전, 설명 정보 출력
 + rpm -qR : 현재 패키지가 의존하는 패키지 목록 출력
 + rpm -ql : 패키지 안의 파일 출력
 + rpm -qd : 문서 파일만 출력
 + rpm -qc : 설정 파일만 출력
 + rpm -qf : 이미 설치된 파일이 어느 패키지에 속한것인지 질의
 + rpm -qpl : 설치되지 않은 패키지 파일안의 파일들을 출력
 + rpm -qpi : 설치할 패키지 파일의 상세 정보를 출력

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
#### 네트워크 포트 확인
- netstat
> 사용방법: netstat -ltup

|옵션|내용|
|---|---|
|-l| netstat에 모든 수신 소켓을 표시한다.|
|-t| 모든 TCP 연결을 표시한다.|
|-u| 모든 UDP 연결을 표시한다.|
|-p| 포트에서 수신하는 어플리케이션/데몬의 이름을 표시한다.|
|-n| 서비스 이름 대신 port 번호를 표시한다.|



- nmap
> 사용방법: nmap [Scan Type(s)] [Options] {target specification}

예시

1. 타켓 선택

* 단일 IP 스캔
> nmap 192.168.100.128

* 특정 IP 스캔
> nmap 192.168.100.128 192.168.100.129

* 호스트 스캔
> nmap www.koromoon.com

* IP 범위 스캔
> nmap 192.168.100.128-254

* 서브넷 스캔
> nmap 192.168.100.0/24

* 텍스트 파일에서 대상 스캔
> nmap -iL koromoon_ip_list.txt


2. 포트 선택

* 단일 포트 스캔
> nmap -p 21 192.168.100.128

* 잘 알려진 포트 스캔
> nmap -p 1-1023 192.168.100.128

* 100 개의 잘 알려진 빠른 포트 스캔
> nmap -F 192.168.100.128

* 서비스 이름으로 포트 스캔
> nmap -p http,https 192.168.100.128

* 모든 포트 스캔
> nmap -p- 192.168.100.128

* 지정된 UDP 및 TCP 포트 스캔
> nmap -p U:53,T:21-25,80 192.168.100.128

3. 스캔 유형

* TCP 연결을 사용하여 스캔
nmap -sT 192.168.100.128

* TCP SYN 스캔을 사용하여 스캔
> nmap -sS 192.168.100.128

* UDP 포트 스캔
> nmap -sU -p 123,161,162 192.168.100.128

* 선택한 포트 검색 (검색 무시)
> nmap -Pn -F 192.168.100.128

4. 서비스 및 OS 감지> 

* OS 및 서비스 감지
> nmap -A 192.168.100.128

* 표준 서비스 감지
> nmap -sV 192.168.100.128

* 공격적인 서비스 감지 (범위는 0 ~ 9, 범위가 높을수록 정확성이 높아짐)
> nmap -sV –version-intensity 5 192.168.100.128

5. 결과물 형식

* 기본 결과물을 파일에 저장
> nmap -oN result.txt 192.168.100.128

* 결과물을 XML 로 저장
> nmap -oX result.xml 192.168.100.128

* 형식화된 결과물로 저장 (Grepable)
> nmap -oG result.txt 192.168.100.128

* 모든 형식으로 결과물 저장
> nmap -oA result 192.168.100.128

- lsof
: 옵션 없이 lsof 명령을 실행하면 시스템에 있는 모든 열린 파일들에 대한 정보를 출력

예시

* 특정 사용자의 열린 파일 출력 , -u 옵션으로 사용자를 지정할 수 있다.
> lsof -u lesstif

* 특정 포트를 사용하는 프로세스 정보 보기, -i 옵션뒤에 프로토콜과 포트를 명시
> lsof -i TCP:22

* 특정 명령어가 사용하는 포트, -c 옵션과 httpd 를 주면 httpd 가 오픈한 파일 정보를 출력
> lsof -c httpd

* 특정 파일을 사용하는 프로세스 보기, 특정 파일을 사용하고 있는 프로세스의 자세한 정보를 볼 수 있다
> lsof /var/log/httpd/access_log

* 특정 디렉터리내 열린 파일 보기, 특정 디렉터리 하위의 열린 파일을 표시하고 싶은 경우가 있다. +D 옵션 뒤에 디렉터리 경로를 명시하면 된다. 아래는 /tmp 디렉터리 밑에 열린 파일을 표시
> lsof +D /tmp

* 포트 범위 지정
> lsof -i TCP:22-80

* IPv4 또는 IPv6 포트만 표시
> lsof -i 4

* 특정 사용자 제외, 사용자 지정 옵션인 -u 에 ^를 추가하면 특정 사용자는 제외할 수 있다
> lsof -u^lesstif -u ^root

* 열린 모든 네트워크 포트 표시
> 모든 포트 lsof -i
 
> TCP 만 lsof -i TCP 
 
> UDP 만 lsof -i UDP


* 특정 프로세스가 오픈한 파일 표시, -p(소문자) 옵션뒤에 PID 를  주면 된다.
> lsof -p 123


***
#### 백업

- cpio

|옵션|내용|
|---|---|
|-o | 표준 출력으로 보내 사용한다. (create)|
| -c| 아카이브 포맷 형식을 'newc'로 지정한다.|
| -d | 필요할 경우 디렉터리를 생성한다.|
| -t | 내용만 확인할 때 사용한다.|
| -F | 표준 입출력전환 기호 대신 파일명을 지정할 때 사용한다.|
| -H [포맷] |아카이브 포맷 형식을 지정한다.|
|-i | 표준 입력으로 받을 때 사용한다. (백업한 자료를 불러올때)|
| -v | 과정을 출력한다.|

사용방법
>]# find /home | cpio -ocv > home.cpio
/home 를 home.cpio 파일로 백업한다.

>]# cpio -icdv < home.cpio
home.spio의 현재 내용을 디렉터리에 복원한다.

- dump
: dump에는 0~9 단계의 레벨을 가지고 있는데, 0단계는 전체백업을 의미하며   
1 ~ 9단계는 부분백업시에 사용하며, 작업정보를 따로 파일에 기록

파일시스템의 설정정보가 들어있는 /etc/fstab을 이용해서 dump 백업이 이루어지며
데이터를 복원할 때는 restore 명령을 사용한다.

dump [파일명] [백업대상]
> -0 ~ -9 : 레벨을 지정한다.
-f : 백업할 매체나 파일명을 지정한다.
-u : 작업 후 /etc/dumpdates 파일에 관련 정보를 기록한다.

사용방법
> ]# dump -0u -f test.dump /dev/sdb1     
/dev/sdb1 를 test.dump 에 전체백업 후 작업정보를 dumpdates파일에 기록한다.

 
> ]# dump -5u -f 1111.dump /home         
/home 디렉터리를 1111.dump 이름으로 5레벨로 백업 후 작업정보를 dumpdates 파일에 기록한다.

- restore       

> restore [백업 파일명]
|옵션|내용|
|---|---|
|-i | 대화식으로 복구할 파일을 선택 후 복원|
|-f | 백업할 매체나 파일명 지정|
|-r | 전체 복원시에 사용|

사용방법

> restore -rf test.dump      
test.dump 파일에 저장된 데이터를 전체 복원한다.

- tar
  
- dd(Disk & Dump)

> *  사용될 수 있는 블럭 크지 지정의 문자들은 다음과 같다:              
> b=512, c=1, k=1024, w=2, xm=number m            
> * --help: 도움말을 보여주고 마친다.                   
> * --version:  버전 정보를 보여주고 마친다.              
> * if=file: 표준 입력 대신에 지정한 file을 입력 대상으로 한다.            
> * of=file: 표준 출력 대신에 지정한 file을 출력 대상으로 한다.  conv=notrunc  옵션을 사용하지 않는 한은, seek= 바이트 크기에 따라 ( seek= 크기가 0아닌 한) 지정한 크기에 따라 출력 파일을 자른다.
> * ibs=bytes: 한번에 bytes 바이트씩 읽는다.   
> * obs=bytes: 한번에 bytes 바이트씩 쓴다.                    
> * bs=bytes:  한번에 bytes 바이트씩 읽고 쓴다.  ibs, obs 값 무시.                             
> * cbs=bytes: 한번에 bytes 바이트씩 변환한다.
> * skip=blocks: 입력 시작에서 blocks 단위 만큼 ibs 크기를 건너띈다.(가령 skip=5, ibs=10 이면, 처음 50바이트를건너띄어 작업을 계속한다.)
> * seek=blocks: 출력 시작에서 blocks 단위 만큼 ibs 크기를 건너띈다.
> * count=blocks: 입력의 blocks 의 ibs 크기만큼만 복사한다.
> * conv=conversion[,conversion…] 대상 파일로 복사할 때, conversion 으로 변환 한다.
>        Conversions:
>> * ascii:  EBCDIC 코드를 ASCII 코드로.
>> * ebcdic: ASCIi 코드를 ebcdic 코드로.
>> * ibm:    ASCII 코드를 alternate EBCDIC 코드로.
>> * block:  cbs 단위로 변환 할 때 읽은 것 안에 있는 줄바꿈 문자들을공 백 문자로 바꾼다.
>> * unblock: cbs 단위로 변환 할 때 읽은 것 안에 있는 공백 문자들을줄바꿈 문자로 바꾼다.
>> * lcase  대문자를 소문자로
>> * ucase  소문자를 대문자로
>> * swab   입력되는 두 바이트의 순서를 바꾼다.  유닉스의 dd 명 령 과  달 리, 이것은 읽혀질 짝이 맞지 않는 추가 바이트 하나가 생겼을 때는그냥 그대로 작업한다.  즉, 입력 파일에서 짝이 맞지 않는 하나의 바이트가 남으면, 마지막 바이트는 그냥 단순히 복사된다.  (이것의 순서가 바뀌지 않는한)
>> * noerror: 오류를 읽어도 계속 한다.
>> * notrunc: 출력 파일을 자르지 않는다.
>> * sync: ibs 블럭 크기와 obs 블럭 크기가 차이가 있을 경우, 그 여백 을 NULL문자로 채운다.



사용방법
> a.txt 파일 안에 있는 문자를 전부 소문자로 변환해서 b.txt파일로 저장한다.          
dd if=a.txt of=b.txt conv=lcase

>/dev/sdb1의 내용을 그대로 /dev/sdc1으로 백업 진행하는데, 블록 크기는 1KB로 지정한다.           
dd if=/dev/sdb1 of=/dev/sdc1 bs=1k


- rsync        
> rsync [백업파일] [백업되는 위치]

|옵션|내용|
|-r | 하위 디렉터리까지 실행|
|-l | 심볼릭 링크를 보존한다.|
|-L | 심볼릭 링크가 가리키는 파일을 복사한다.|
|-p | 권한을 보존한다.|
|-t | 타임스탬프를 보존한다.|
|-g | 그룹소유권을 보존한다.|
|-o | 소유권을 보존한다.|
|-H | 하드링크 보존|
|-v | 진행상황을 출력|
|-u | 업데이트된 내용만 전송|
|-z | 전송할 때 압축|
|-b | 백업시 동일한 파일이 존재하는 경우 ~를 붙여서 생성한다.|
|-a | -rlptgoD를 한번에 실행할 때 사용 (--archive)|
|--progress | 파일이 전송되는 동안 전송 상황정보를 출력한다.|

사용방법
> rsync -av /home /home5     
/home를 그대로 유지하면서 /home5로 백업한다.

> rsync -avz 192.168.2.20:/test /backup    
192.168.2.20로 원격접속후 /test 파일을 압축해서 /backup로 복사한다.

> rsync -avz -e ssh other@192.168.2.20:/test ~/backup    
-e옵션은 원격지에 접속시 프로토콜을 지정한다.  (최근에는 생략하면 자동으로 ssh를 사용한다.)    
 192.168.2.20 원격지로 other 계정으로 접속 후 test 파일을 압축해서 복사한다.    

***
#### firewall-cmd

- 방화벽 실행 여부 확인, 실행 중이면 running, 실행 중이 아니면 not running을 출력합니다.
> firewall-cmd --state

<br>

- 방화벽 다시 로드, 방화벽 설정 후 다시 로드해야 적용됩니다.
> firewall-cmd --reload

<br>

- 존(Zone) 출력하기
> firewall-cmd --get-zones

<br>

- 존(zone) 목록을 출력합니다.
> firewall-cmd --get-default-zone

<br>

- 기본 존을 출력합니다, 활성화된 존을 출력합니다.
> firewall-cmd --get-active-zones

<br>

##### 사용 가능한 서비스/포트 출력하기
사용 가능한 모든 서비스/포트 목록을 출력합니다.public 존에 속한 사용 가능한 모든 서비스/포트 목록을 출력합니다.
> firewall-cmd --list-all

> firewall-cmd --zone=public --list-all


##### 서비스/포트 추가/제거
* ftp 서비스를 추가합니다.
> firewall-cmd --add-service=ftp

* ftp 서비스를 제거합니다.
> firewall-cmd --remove-service=ftp

* 21 tcp 포트를 추가합니다.
> firewall-cmd --add-port=21/tcp

* 21 tcp 포트를 제거합니다.
> firewall-cmd --remove-port=21/tcp



--zone=<zone>
zone 옵션이 없으면 기본 존(default zone)에 추가 또는 삭제를 합니다. 다른 존에 작업하고 싶으면 zone 옵션을 추가합니다. 예를 들어

firewall-cmd --zone=trusted --add-service=ftp
는 trusted 존에 ftp 서비스를 추가합니다.

--permanent
시스템 재부팅 또는 방화벽 재시작 후에도 적용되도록 하려면 --permanent 옵션을 붙입니다.

firewall-cmd --permanent --add-service=ftp


***
#### 패스워드 관리

##### passwd [OPTION] 사용자계정

|옵션|의미|
|---|---|
|-a|-S옵션과 같이 쓰인다. all이라는 뜻으로 -Sa하면 모든 사용자에 대한 패스워드 정보를 보여준다.|
|-S (--status)|사용자에 대한 패스워드 정보를 알 수 있다.|
|-l|사용자의 패스워드에 잠금(lock)을 걸어 로그인을 막는다(--lock)|
|-u|사용자에게 설정되어 있는 패스워드 잠금을 푼다 (--unlock)|
|-d|사용자의 패스워드를 제거한다. 패스워드 없이 로그인이 가능하다|
|-n|패스워드 변경까지의 최소 날짜를 설정한다. 즉, 패스워드 변경 후 최소로 사용해야 되는 날짜수이다.|
|-x|현재 패스워드의 유효기간을 지정한다. 즉 패스워드 최대 사용 가능한 날짜수이다.|
|-w|패스워드 만료 전 경고 날짜를 지정한다.|
|-i|패스워드 만료된 뒤에 사용자 계정 사용이 실제 로그인이 불가능하게 되기까지의 유예기간을 설정한다.|
|-e| 다음 로그인 시에 반드시 패스워드를 변경하도록 할 때 사용한다.|
	
#####   chage [OPTION] 사용자계정
	
|옵션|의미|
|---|---|
|-l|사용자의 패스워드에 대한 정보를 보여준다.|
|-d (--lastday)|최근 패스워드를 바꾼 날을 수정합니다.|
|-m|패스워드 변경의 최소 날짜를 지정합니다. 즉 패스워드 변경 후 일정기간 사용해야될 최소 만기일을 지정합니다.|
|-M|패스워드 변경 없이 사용 가능한 최대 날짜를 지정합니다. 즉 한 번 설정한 패스워드의 만기일을 지정합니다.|
|-I|패스워드 최대 사용기간 만료 후에 실제 패스워드 잠금을 설정하기까지의 유예기간을 지정합니다.|
|-E|계정이 만기되는 날을 지정합니다. MM/DD/YY 또는 YYYY-MM-DD 형태로 지정.|
|-W|패스워드 만료 전 변경을 요구하는 경고 날짜를 지정합니다.|
	

##### passwd 와 chage 비교
|비교|passwd|chage|
|---|---|---|
|패스워드 정보 보기|-S|-l|
|패스워드 변경 날짜 설정|없음|-d|
|패스워드 변경 최소 날짜 설정|-n|-m|
|패스워드 변경 최대 날짜 설정|-x|-M|
|패스워드 변경 경고 기간 설정|-w|-W|
|패스워드 유예기간 설정|-i|-I|
|계정 만기 설정|없음|-E|
	

#### crontab

> crontab [option] 파일명/유저(-e 사용시)

|옵션|내용|
|---|---|
|-l|crontab에 설정된 내용을 출력(조회)|
|-e|crontab의 내용을 작성하거나 수정(설정)|
|-r|crontab 내용 삭제(삭제)|
|-u|특정 사용자의 crontab 파일 설정시, 다른 명령어와 같이 사용하여 crontab 조회 및 수정|
|-i|crontab 내용 삭제전 확인 문구 띄우기|


***
#### 모듈 관련 작업 및 커널

* lsmod    
: 리눅스 커널에 적재된 모듈 정보를 출력하는 명령

* insmod    
: 커널에 모듈을 적재하는 명령. 해당 모듈 파일이 존재하는 디렉터리까지 이동, 의존성이 있는 모듈인 경우에는 적재 못함.

> [사용법]        
$ insmod 모듈_파일명

* rmmod
커널에서 모듈을 제거하는 명령.

> [사용법]       
$ rmmod 모듈

* modprobe
리눅스 커널에 모듈을 적재하거나 제거하는 명령. 단일모듈, 의존성이 있는 여러 모듈, 특정 디렉터리의 모든 모듈들을 적재할 수 있음.

> [사용법]   
$ modprobe [option] 모듈 [기호=값]

옵션
>-l : 사용 가능한 모듈 정보
-r : 의존성있는 모듈들을 찾아 사용안하면 제거
-c : 모듈 관련 환경 설정 파일의 내용을 전부 출력


* modinfo
모듈 파일에 대한 정보 출력
<br>
* 모듈 관련 설정 파일
부팅 시에 특정 모듈을 커널에 적재할 때 /etc/modprobe.d 디렉터리안에 '.conf'로 끝나는 모든 파일 인식
<br>
* 모듈 의존성 파일 : modules.dep
모듈 간의 의존성을 기록해둔 파일로 '/lib/modules/커널버전/modules.dep' 디렉터리 안에 위치.
이 파일 갱신하고 관리해주는 명령은 depmod
\# depmod는 모듈 간의 의존성을 관리하는 명령어로 위 파일과 맵 파일을 생성한다.

* 커널
시스템 자원을 소유하고 관리하는 역할           
하드웨어, 메모리, 프로세스 스케줄링을 담당하고 프로그램이 하드웨어 자원을 간접적으로 접근할 수 있도록 해줌.           
'uname -r' 명령으로 커널 버전 확인 가능              
	
<br>
	
* 커널 컴파일
커널 컴파일은 커널 소스를 다운로드하여 사용하는 시스템에 최적화된 커널을 만드는 과정.
	
<br>
	
ㅁ  커널 컴파일 순서

> 1. 커널 소스파일 압축해제
> 2. 커널 소스의 설정 값 초기화(make mrproper)
> 3. 커널 컴파일 옵션 설정 작업(make menuconfig)
> 4. 커널 이미지 파일 생성 작업(make bzImage) : bzip2로 압축함.
> 5. 커널 모듈 생성을 위한 컴파일 작업(make modules)
> 6. 커널 모듈 설치 작업(make modules_install) : '/lib/modules/커널버전' 디렉터리 안에 복사.
grub.conf 파일 수정(make install)
새로운 커널 사용을 위한 시스템 재부팅


ㅁ Make Cleaning Target

> - make clean : 생성된 오브젝트 파일을 제거하고 다시 configure할 때 사용
> - make mrproper : configure 작업을 통해 생성된 오브젝트 파일 뿐만 아니라 config 파일, 다양한 백업 파일 등도 제거. 주로 사용하는 명령어.
> - make distclean : mrproper 작업과 더불어 편집된 백업 및 패치 파일도 모두 제거

ㅁ 커널 컴파일 주요 도구

> - make config : 텍스트 기반 설정 도구. y, m, n으로 설정
> - make menuconfig : 컬러 메뉴 제공, 커서 이용. 가장 많이 사용.
> - make nconfig : 컬러 메뉴, F1~9 기능키 제공.
> - make xconfig : X 윈도 환경의 Qt 기반의 설정 도구
> - make gconfig : X 윈도 환경의 Gtk+ 기반의 설정 도구

***
#### 로그인 재부팅 로그 확인
- last   var/log/wtmp 로그인과 재부팅 로그
- lastb  var/log/btmp 접속 실패 기록

|옵션|설명|
|---|---|
|-num|num만큼의 줄만 출력|
|-n num| -num과 동일|
|-R|hstname 필드는 출력 안함|
|-a|출력되는 목록에서 hostname 필드는 마지막에 출력|
|-d|다른 host에서 접속한 내용만 출력|
|-x|shutdown 상태 및 runlevel이 바뀐 상태 출력|
|-f file| 지정한 file에서 정보를 읽어와서 출력|

- lastlog /var/log/lastlog 로그 파일의 정보를 분석하여 출력

|옵션|Long옵션|설명|
|---|---|---|
|-t DAYS|--time DAYS|지정한 날짜 단위 기간에 로그인한 정보만 출력|
|-u LOGIN|--user LOGIN|지정한 로그인명의 lastlog 정보만 출력|
|-h| --help|lastlog 명령어 사용법 출력|


- logger /var/log/message 파일에 원하는 메세지 기록

|옵션|설명|
|---|---|
|-i|각각의 라인마다 logger의 프로세스 ID 기록|
|-s|시스템 로그뿐만 아니라 표준 출력으로도 메세지 출력|
|-f file|지정한 file에 로그를 출력|
|-p pri|pri(우선순위)를 메세지와 함께 출력, 기본값은 user.notice|
|-t tag| tag를 각각의 라인마다 기록|

***
#### htpasswd    
: 아파치 패스워드 걸기
사용방법
> htpasswd [ -c ] [ -m ] [ -D ] passwdfile username

> htpasswd -b [ -c ] [ -m | -d | -p | -s ] [ -D ] passwdfile username password

> htpasswd -n [ -m | -d | -s | -p ] username

> htpasswd -nb [ -m | -d | -s | -p ] username password


|옵션|설명|
|---|---|
|-b|배치(batch) 모드를 사용한다. 예를 들어, 암호를 물어보지않고 명령행에서 받는다. 명령행에 암호가 직접 드러나므로, 이 옵션은 매우 조심해서 사용해야 한다.|
|-c|passwdfile을 만든다. passwdfile이 이미 존재한다면, 덮어쓴다. 이 옵션을 -n 옵션과 같이 사용할 수 없다.|
|-n|파일을 수정하지않고 결과를 표준출력으로 출력한다. 아파치가 문서이외의 곳에 생성한 암호를 저장할때 유용하다. (항상 첫번째 아규먼트인) passwdfile 아규먼트가 없기때문에 명령행 문법이 다르다. -c 옵션과 같이 사용할 수 없다.|
|-m|MD5를 사용하여 암호를 암호화한다. Windows, Netware, TPF에서 기본값이다.|
|-d|crypt()를 사용하여 암호를 암호화한다. Windows, Netware, TPF를 제외한 모든 플래폼에서 기본값이다. 모든 플래폼의 htpasswd가 이 형식을 지원할 수는 있지만, Windows, Netware, TPF의 httpd 서버는 이 형식을 지원하지 않는다.|
|-s|암호를 SHA 암호화한다. LDAP 디렉토리교환형식(ldif)을 사용하여 Netscape 서버로 정보를 보내거나 거져올때 유용하다.|
|-p|암호를 그대로 사용한다. 모든 플래폼의 htpasswd가 지원하지만, Windows, Netware, TPF의 httpd 데몬만이 일반 암호를 받는다.|
|-D|사용자를 삭제한다. htpasswd 파일에 사용자명이 있다면 삭제한다.|

- passwdfile
사용자명과 암호를 저장하는 파일명. -c를 사용한 경우 파일이 없다면 새로 만들고, 있다면 덮어쓴다.

- username
passwdfile에 만들거나 수정할 사용자명. username이 이 파일에 없다면 항목을 추가한다. 있다면 암호를 수정한다.

- password
암호화하여 파일에 저장할 암호. 오직 -b 옵션과 같이 사용할 수 있다.

***
#### ssh

1) 경로 : /usr/bin/ssh

 2) 요약 : Open SSH 원격 로그인 클라이언트

 3) 사용 방법 : ssh [옵션] [주소]

|옵션|내용|
|---|---|
|-1 | ssh를 프로토콜 버전 1로 시도|
|-2 | ssh를 프로토콜 버전 2로 시도|
 |-4 | IPv4 주소만 사용|
| -6 | IPv6 주소만 사용|
| -F configfile| 사용자 설정 파일(configfile)을 지정|
| -I(아이) smartcard_device | 사용자 개인 RSA 키를 저장한 디바이스(smartcard_device)를 지정|
| -i identity_file | RSA나 DSA 인증 파일(identity_file)을 지정|
| -l login_name | 서버에 로그인할 사용자(login_name)을 지정|
| -p port | 서버에 접속할 포트를 지정|
| -q | 메시지를 출력하지 않음|
| -V | 버전 정보를 출력|
| -v | 상세한 정보를 출력. 디버깅에 유용|
| -X | X11 forwarding 기능을 활성화. 이는 서버에 접속하여 서버의 프로그램을 클라이언트의 화면에서 실행할 수  있음|
| -x | X11 forwarding 기능을 비활성화|
| -y | 신뢰할만한 X11 forwarding 기능을 활성화|

***
#### IPTABLE

iptables의 RULE은 크게 **Table & Chain, Match, Target** 개념을 사용한다.

##### (1) Table & Chain
Table 은 Filter, NAT, Mangle, Raw 네 가지로 구분
1) Filter

가장 기본적인 테이블로, 패킷 필터링을 담당한다.

아래 세 가지의 기본 Chain 을 가지고 있다.

a. INPUT : 호스트 컴퓨터의 로컬 프로세스를 향해 들어온 모든 패킷이 방문하는 chain 이다.

b. FORWARD : 타겟이 호스트가 아닌, 즉 호스트 컴퓨터를 경유하는 패킷이 방문하는 chain 이다. 이 chain 을 방문하는 packet 은 local process 를 거치지 않는다.

c. OUTPUT : 호스트 컴퓨터에서 자체적으로 생성된 패킷이 방문하는 chain 이다.


2) NAT

Network Address Translation 의 약자로,

ip와 port 등을 변환하는 역할을 한다.

주로 Port forwarding, Bridging, Packet Monitoring 에 사용되며

실제로 실무에서 대부분의 서비스는 클라이언트의 ip, port 를 내부 프로그램으로 돌릴 때 NAT 테이블을 사용한다.

 

NAT 테이블의 기본 chain은 4가지가 있다.

a. PREROUTING : 패킷을 INPUT rule 로 보내기 전 ip 와 port를 변경하는 역할을 한다.

b. INPUT : filter 테이블과 같은 역할이나, 먼저 적용

c. OUTPUT : filter 테이블과 같은 역할이나, 나중에 적용

d. POSTROUTING : 패킷이 OUTPUT rule 에서 나온 이후 ip 와 port 를 변경하는 역할을 한다.

 

꼭 PREROUTING 이 destination 만을, POSTROUTING 이 source 만을 변경하라는 법은 없으나, 그러한 방식을 주로 사용한다.

 

3) Mangle : 특수 규칙을 이용해 패킷 구조를 변경하거나 ToS를 설정한다.

4) Raw : Netfilter API의 기본 tracking 과 별개로 존재하는 룰을 만들 때 사용된다.

5) Security : 보안을 위한 Table


##### (2) Match

##### (3) TARGET
iptalbes에서 패킷이 규칙과 일치할 때 동작을 취하는 타겟을 지원합니다.

1) ACCEPT

패킷이 해당 rule에 매치되면 해당 패킷을 통과시킨다.

패킷이 해당 스테이지에서 통과되면 다음 스테이지로 넘어간다.

예를 들어, NAT의 PREROUTING 단계를 통과한 패킷은 FILTER 의 INPUT 단계로 넘어간다.

 
2) DROP

패킷이 해당 rule에 매치되면 해당 패킷을 버린다.


3) REJECT

기본적으로 DROP 과 같으나, 거절되었음을 응답하는 과정이 추가된다.

가령 ping 을 쏘는 ICMP 프로토콜의 경우 DROP은 무응답으로 timeout 되는 blackhole 상태이나, REJECT는 "port unreachable" 이라는 메시지를 mirror 해준다.

LAN을 구성할 때 IP occupy 를 신경써야하는 상황에서는 DROP 보다는 REJECT 가 더 좋을 것이다.

* --reject-with : icmp-proto-unrachable 등 reject 타입을 정한다.

 
4) LOG

matching packet 에 대한 kernel logging 을 켜 dmesg 나 syslog 로 보여준다.

옵션으로 아래 내용을 전달할 수 있다.

* --log-level

* --log-prefix

LOG 는 내부적으로 RETURN 이 아닌 CONTINUE 를 전달한다.


**iptables-extensions** 에서 정의되는 target 중 유용한 것은 다음이 있다.

 

1) AUDIT

accept, drop, reject 패킷을 기록할 때 사용된다.

iptables -N AUDIT_DROP # new chain
iptables -A AUDIT_DROP -j AUDIT
iptables -A AUDIT_DROP -j DROP
 

2) DNAT

NAT table 의 PREROUTING, OUTPUT chain 에서만 작동한다.

Packet의 Destination address 를 새로운 ip:port 로 수정한다.

iptables (...) -p tcp -j DNAT --to-destination 192.168.10.111:2323
 

3) IDLETIMER

특정 NIC가 유휴상태임을 구별하는 데 쓰인다.

설정한 타이머가 expired 되면 sysfs notification 을 유저에게 보낸다.

유저는 이 정보를 활용해 절전모드 off 등을 구별할 수 있다.

 

4) LED

system indicator 이 특정 패킷에 반응하도록 설정할 수 있다.

* --led-trigger-id : 트리거 이름을 설정한다. 실제 이름은 netfilter-(설정한이름) 이 된다.

* --led-delay : ms 단위 illuminate time 설정

iptables -A INPUT -p tcp --dport 22 -j LED --led-delay 100 --led-trigger-id ssh
echo netfilter-ssh >/sys/class/leds/ledname/trigger
 

5) MASQUERADE

NAT 테이블의 POSTROUTING chain 에서만 사용할 수 있는 target으로,

패킷이 나가는 인터페이스와 IP를 매핑하는 역할을 한다.

주의점으로는

a. interface 가 down 되면 connection 이 끊긴다.

b. DHCP 연결에서만 사용 가능하며, static 의 경우 SNAT 을 사용해야한다.

c. tcp, udp, dccp, sctp 에서만 사용 가능하다.

 

* --to-port : 사용할 source port 를 정하여 default SNAT 을 덮어씌운다.

* --random. --random-fully : 포트 매핑을 랜덤하게 한다.

 
 

6) REDIRECT

NAT 테이블의 PREROUTING, OUTPUT chain 에서만 사용할 수 있는 chain 이다.

기기 내에서 Destination IP 를 incoming interface 의 primary address 로 바꿈으로써 패킷을 redirect 해준다.

 

* --to-port : 사용할 destination port

* --random : port mapping 을 randomize 한다.


1) SET

ipset 명령어에 정의되어있는 옵션 이것저것을 추가할 수 있다.


8) SNAT

NAT 테이블의 POSTROUTING, INPUT chain 에서만 사용 가능하다.

Packet의 Source address 를 새로운 ip:port 로 수정한다.

자세한 내용은 DNAT, MASQUERADE 와 함께 후술한다.

 

9) TEE

패킷을 clone 하고 local network segment의 다른 호스트에게 redirect 한다.

즉, 받은 패킷을 처리하는 동시에 mirroring 해주는 기능이다.

 

아래는 eth2 로 받은 tcp 80포트 패킷을 192.168.0.1 에 mirror 해주는 설정 예시다.

> iptables -A PREROUTING -t mangle -i eth2 -p tcp --dport 80 -j TEE --gateway 192.168.0.1
 

10) TTL

IPv4의 TTL 헤더를 수정한다.

TTL 헤더는 time to live 가 초과될 때 까지 packet이 traverse 할 수 있는 router의 수다.

TTL 을 줄이면 패킷은 금방 사라질 것이며, TTL을 늘리면 더 오래 살아남을 수 있다.

iptables-extensions man page 에서는 local network 를 벗어나는 packet 에는 절대로 set 이나 increment 하지 말라고 한다.





##### 커맨드와 옵션

ㅁ Rule 관련

|옵션|내용|
|---|---|	
|-A (–-append) | 새로운 규칙을 추가|
|-C (–check) | 패킷을 테스트|
|-D (–delete) | 규칙을 삭제|
|-F (–flush) | chain 으로 부터 규칙을 모두 삭제|
|-I (–Insert) | 새로운 규칙을 삽입|
|-L (–list) | 규칙을 출력|
|-N (–new) | 새로운 chain을 만듬|
|-P (–policy) | 기본정책을 변경|
|-R (–replace) | 새로운 규칙으로 교체|
|-X (–delete-chain) | chain 을 삭제|
|-Z (–zero) | 모든 chain의 패킷과 바이트 카운터 값을 0으로 만듬|


ㅁ option 관련
: iptables에서 패킷을 처리할 때 만족해야 하는 조건을 가리킵니다.    
즉, 이 조건을 만족시키는 패킷들만 규칙을 적용합니다.

|옵션|내용|
|---|---|
|-s (–source) | 출발지 IP주소나 네트워크와의 매칭|
|-d (–destination) | 목적지 IP주소나 네트워크와의 매칭|
|-p (–protocol) | 특정 프로토콜과의 매칭|
|-i (–in-interface) | 입력 인터페이스|
|-o (–out-interface) | 출력 인터페이스|
|–state | 연결상태와의 매칭|
|–comment | 커널 메모리 내의 규칙과 연계되는 최대 256바이트 주석|
|-y (–syn) | SYN 패킷을 허용하지 않음|
|-f(–fragment) | 두번째 이후의 조가게 대해서 규칙을 명시한다.|
|-t (–table) | 처리될 테이블|
|-j (–jump) | 규칙에 맞는 패킷을 어떻게 처리할것인가를 명시한다.|
|-m (–match) | 특정 모듈과의 매치|
