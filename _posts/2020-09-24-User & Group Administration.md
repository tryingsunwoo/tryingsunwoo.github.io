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

#### 계정이란?
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
