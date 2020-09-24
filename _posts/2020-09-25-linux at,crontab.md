---
title: "at, crontab"
excerpt: "스케쥴링 관리"
toc: true
toc_sticky: true
categories:
- linux
tags:
- linux
last_modified_at: 2020-09-24

---
## 스케쥴링 관리
- 반복적인 작업 : 정기정검 파일백업, 디스크 용량 확인 등등...
- 비반복적인 작업 : 업데이트, 재부팅 등등...

**반복과 비반복에 따라 사용되는 명령어가 다르다.** 

### at 명령어
* 비반복에 사용되는 명령어
* 지정한 시간에 해당 작업이 실행될 수 있도록 작업 스케쥴을 예약 처리해주는 기능

**at 스케줄을 사용하기 위해서는 at 데몬이 작동하고 있어야한다**
```console
# systemctl restart atd
# systemctl status atd /* 상태 확인 */
```

- systemctl 이란? -> systemd를 사용하게 되면서 서비스, 부팅 시스템 전반에 걸친 관리를 위해 사용되는 명령어
```console
# systemctl start daemon      : 서비스 시작
# systemctl stop daemon       : 서비스 중지
# systemctl restart daemon    : 서비스 재시작
# systemctl status daemon     : 서비스 상태 확인
```

#### at 명령어 형식
**# at [time]**
```console
[root@server1 ~]# date                       /* 현재시간 확인 */
Thu Sep 24 20:10:32 KST 2020                 /* 현재 시간 */   
[root@server1 ~]# tty                        /* 내가 사용중인 터미널 확인 */
/dev/pts/0
[root@server1 ~]# at 2011                    /* 20시 11분에 */
warning: commands will be executed using /bin/sh
at> echo 111 > /dev/pts/0                    /* /dev/pts/0에 echo 111을 예약 */   
at> <EOT>
job 10 at Thu Sep 24 20:11:00 2020
[root@server1 ~]# at -l                      /* 예약 리스트 보기 */
9       Fri Sep 25 20:09:00 2020 a root
10      Thu Sep 24 20:11:00 2020 a root
[root@server1 ~]# atrm 9                     /* 예약 삭제 */
[root@server1 ~]# at -l
10      Thu Sep 24 20:11:00 2020 a root
[root@server1 ~]# 111                        /* 결과 */
```

### crontab 명령어
* 반복적인 작업을 위하여 특히 매일, 매주 , 매월 등 특정 일자 시간에 반복되는 작업을 정확한 시간에 제어하기 위한 기술

* cron 데몬은 같은 작업을 주기적으로 반복 실행되도록 하는 **작업 스케줄용 데몬** 으로, 매분, 매시, 매일, 매주, 매월, 매요일 단위로 cron 데몬에 의해서 작업이 실행될 수 있음.

* /etc 디렉토리에 시, 일, 주, 월, 요일 단위 형태로 cron 디렉토리가 존재하고, 이들 디렉토리 안에는 작업 예약 스크립트들이 들어 있음. /etc/cron/daily 디렉토리 안에는 여러 가지 스크립트들이 존재하고, 매일 이들 스크립트는 cron 데몬에 의해서 자동으로 실행됨.

cron으로 작업 스케줄 예약 위해서는 crond 데몬을 작동시켜야함
```console
[root@server1 ~]# systemctl restart crond
[root@server1 ~]# systemctl enable crond
[root@server1 ~]# systemctl is-enabled crond
enabled
```
![](https://github.com/tryingsunwoo/tryingsunwoo.github.io/blob/master/assets/image/crontab/crontab.png?raw=true)


#### crontab 명령어 형식
```console
# crontab [-u user] file
# crontab [-u user] [ -e | -l | -r ]
```
* 옵션
-e (edit user is crontab)
-l (list user is crontab)
-r (delete user is crontab)
-i (prompt before deleting user is crontab)
-s (selinux context)

##### crontab 예시
```console
[root@server1 test]# crontab -e
* * * * * touch /test/file{1..5}
~
~
~
~
~
:wq


[root@server1 test]# crontab -l
* * * * * touch /test/file{1..5}
[root@server1 test]# ls
file1  file2  file3  file4  file5
[root@server1 test]# crontab -r
[root@server1 test]# crontab -l
no crontab for root
[root@server1 test]#
```

##### crontab 활용

1. 매분 실행되는 스크립트 
```console
* * * * * /root/bin/check.sh
```
2. 특정 주기(직접 선택하여) 실행되는 스크립트
```console
20,37 * * * * /root/bin/check.sh
11,16,22,48,55 * * * * /root/bin/check.sh
```
3. 주기적으로 실행되는 스크립트
```console
*/7 * * * * /root/bin/check.sh
* */3 * * * /root/bin/check.sh
```
3-1 주기적으로 실행되는 스크립트의 시간을 항상 동일하게 맞추기
기존의 방식으로 실행을 하게 되면 crond 재시작, crontab re-install , 재부팅, 서버 작업후 부팅 등의 여러 이유에 따라 시작시간이 달라진다. 
이런 경우에 생길수 있는 문제점은 스크립트 자체는 실행에 문제가 없다.
하지만.. 파일의 생성 시간, 로그의 timestamp등이 뒤죽박죽이다 : 데이터 정렬 및 분포도를 제대로 끌어낼수 없다.
고정적인 값으로 나오게끔 변경
```console
0-59/7 * * * * /root/bin/check.sh
* 0-23/3 * * * /root/bin/check.sh : 주의점 > 정각에 실행되는 
```
스크립트와 병목현상이 일어 날 수 있다. 8 0-23/3 * * * 


4. 분 시에 맞추어 주기적으로 실행되는 스크립트
```console
20 16 * * * /root/bin/check.sh
20,40 */2 * * * /root/bin/check.sh
```

5. 주중에만 오후 백업을 실행한다. 오후 백업 시간 : 21:05분 
조건 : 
명령어 /root/bin/check.sh
주중 월화수목금
시 21시
분 5분

```console
5 21 * * 1-5 /root/bin/check.sh
```
