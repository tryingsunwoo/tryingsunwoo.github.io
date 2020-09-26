---
title: "User & Group Administration"
excerpt: "사용자 그룹 관리"
toc: true
toc_sticky: true
categories:
- linux
tags:
- linux
last_modified_at: 2020-09-23

---
## 계정관리
### 계정이란?
* 신원 증명
이 시스템에 권한이 있는 사용자가 특정 자원에 대해 접근할 때 이 사람이 접근할 수 있는지를 계정으로 구분하기 위해서
* 그룹 - 큰 의미의 신원증명, 권한에 대하여 좀 더 한번에 설정을 할 수 있는 특수권한.

### 사용자 관리 (User Administration)
#### 사용자 정보 파일
```console
/etc/passwd     /* 사용자의 기본적인 정보가 저장된 파일 */
/etc/shadow     /* 사용자의 암호가 저장된 파일 */
```
/etc/passwd 파일의 필드를 해석해보자
```console
[root@server1 ~]# cat /etc/passwd | head -1
```
해당 명령어를 실행하면
```console
root:x:0:0:root:/root:/bin/bash
```
라고 출력이 되는데 이 출력문을 해석해보면
root      : 접속시 사용하는 문자열 이것으로 계정을 구분한다(사람)
x         : 과거 비밀번호 필드 였던 곳
0         : UID 이것으로 계정을 구분한다(컴퓨터)
0         : GID 이것으로 계정을 구분한다(컴퓨터)
root      : 코멘트 - 해당 계정에 대한 어떠한 문자열을 작성해 둘 수 있는곳
/root     : 계정의 홈 디렉토리를 지정
/bin/bash : 계정이 사용할 쉘을 지정

```console
# cat /etc/shadow | grep root
root:$6$H7a443TeW/KN2Auo$Ki3t2O0FHRrEkQ00uMxTQbyfD4r2buRp629Epkn3OLoHcEYSm1ufFa.YMh2cXk6fj442oYlWtjf4xaK/44kIv.:18527:0:99999:7:::
```
* /etc/shallow 파일의 필드 해석

필드         설  명
root        사용자 이름(login name)
$6$5N2Onj~  사용자 암호(encrypted password)
18351       암호변경일(days since Jan 1, 1970 that password was last changed)
0           암호를 변경할 수 없는 최소 날짜 (days before password may be changed)
99999       암호를 사용할 수 있는 최대 날짜 (days after which password must be changed)
7           사용자 암호 만료되기전 사용자에게 경고 메세지를 주는 기간 (days before password is to expire that user is warned)
            사용자 로그인하지 않으면 암호를 비활성화 시키는 기간 (days after password expires that account is disabled)
            사용자 암호 만료 기간, 절대로 넘을 수 없는 기간 (days since Jan 1, 1970 that account is disabled)
            아직 기능이 없음

암호필드 추가
$[숫자]$[salt]$[hash 결과]
HASH 종류
$1 : MD5
$2 : 
$5 : SHA-256
$6 : SHA-512
salt : 동일한 문자열로 암호를 저장하더라도 같은 암호화 값을 가지지 않도록 변경해주는 역할
hash 결과 : hash 종류와 salt 값으로 구성되어 있는 결과 값

pwunconv > /etc/shadow에 저장된 암호를 /etc/passwd로 이동
pwconv > /etc/passwd에 저장된 암호를 /etc/shadow로 이동

#### 사용자 관리 명령어
```console
useradd CMD     /* 사용자 정보 추가 명령어 */
usermod CMD     /* 사용자 정보 변경 명령어 */
userdel CMD     /* 사용자 정보 삭제 명령어 */
```
##### useradd 명령어
* adduser도 가능함

```console
[root@server1 ~]# groupadd -g 62000 g1   : 그룹생성 GID를 62000번으로 고정 부여
-g 주 그룹을 설정하는 경우에 사용

[root@server1 ~]# cat /etc/group | grep g1 
g1:x:62000:         : ㅎgid가 62000인 그룹 g1을 만듦

[root@server1 ~]# useradd -u 2000 -g g1 test2
[root@server1 ~]# useradd -G g1 test3
-G 지정된 그룹으로 가입을 시키는 경우에 사용하는 옵션

[root@server1 ~]# useradd -c "Test account test5" test5
-c 계정에 무언가 메모를 남기듯이 사용하는 옵션

[root@server1 ~]# useradd -u 1005 test7
UID 1005번으로 지정
```

* 계정을 생성할때 기초 파일 생성 위치
```console
[root@server1 ~]# ls -ld /etc/ske
drwxr-xr-x. 3 root root 92  9월 24 01:14 /etc/skel
```
추가로 파일을 생성하여 skel 디렉토리에 복사 되는지 확인
```console
[root@server1 skel]# pwd
/etc/skel
```

```console
[root@server1 skel]# echo "skel dir TEST" > file1.txt
[root@server1 skel]# useradd skeluser
[root@server1 skel]# echo 'skeluser' | passwd --stdin skeluser
[root@server1 skel]# ssh skeluser@localhost
[skeluser@server1 ~]$ ls
file1.txt

[skeluser@server1 ~]$ exit
[root@server1 ~]# cp -r /etc/skel/ /etc/skel2
[root@server1 ~]# cd /etc/skel2
[root@server1 skel2]# cp file1.txt file2.txt
[root@server1 skel2]# useradd -mk /etc/skel2 skel2
```
skel 디렉토리를 지정하여 계정 생성
/etc/skel 디렉토리에 존재하는 파일들의 경우에는 계정을 생성하면서 여러가지 계정에 필요한 파일들을 자동 복사해준다. 파일의 권한은 생성되는 게정의 권한으로 전환된다.

SKEL에 필요한 디렉토리를 여러가지 만들어두고 필요한 경우 변경해서 사용하면 편하다.
EX : 관리자 계정용 SKEL , 일반 계정용 SKEL

```console
[root@server1 ~]# useradd -D  : 기본값 파일
```

##### usermod 명령어
* 계정의 정보를 변경하는 경우에 사용하는 명령어
* 주요 옵션
```console
usermod -u 2000 user01
usermod -s /bin/ksh user01
usermod -g 10 -c "Test Group" user01 
usermod -l user03 -d /home/user03 -m user01  (-l : login name, -m : move directory)
```

##### userdel 명령어
* 더이상 해당 시스템에서 사용이 되지 않는 경우에 사용자 삭제
ex) 퇴사, 서비스종료, 밴, 프로젝트 계정의 경우 프로젝트 종료 시점에 삭제

```console
userdel user01       /* /etc/passwd, /etc/shadow 정보 삭제 */
userdel -r user01    /* /etc/passwd, /etc/shadow + Home Directory 삭제 */
```

#### 그룹관리
* 그룹 정보 파일위치 /etc/group
##### group추가
```console
groupadd class1
groupadd -g 1000 class1
```
##### group 정보 변경
groupmod
```console
[root@server1 ~]# groupmod -g 61235 class1
GID 번호 변경
[root@server1 ~]# groupmod -n g1 class1
이름 변경
```
##### group 삭제
* groupdel
```console
[root@server1 ~]# groupdel g1
```