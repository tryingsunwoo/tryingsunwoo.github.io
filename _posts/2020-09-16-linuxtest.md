---
title: "grep, find pratice"
excerpt: "grep , find 연습"
categories:
- linux 기본
tags:
- linux
last_modified_at: 2020-09-15
---
## grep 명령어 연습
# grep OPTIONS PATTERN ARG1
* grep -l 검색문자열 경로  여러 파일 중 검색 문자열 존재 파일 출력
* grep -n 검색문자열 경로  파일에서  검색 문자열 제외하고 라인 번호도 출력
* grep -v 검색문자열 경로  검색문자열 제외하고 나머지 출력
* grep -i 검색문자열 경로  단어 일부분 아니고 단어 전체 일치할 때 출력
* grep -w 검색문자열 경로  단어 일부분 아니고 전체가 일치하는 경우 출력

# 패턴 사용법
* grep 'ro*t'   모든 문자를 포함
* grep 'no...y' .에 하나의 영문자나 숫자를 치환
* grep '^root'  라인의 시작이 root인 라인을 검색
* grep 'root$'  라인의 끝이 root로 끝나는 라인을 검색
* grep 'user0[123]' user0으로 시작해서 1이나 2나 3으로 끝나는 문자열 검색

* 여러개 패턴 검색하기
egrep '(pattern1|pattern2|pattern3)'

## find 명령어 연습
**디렉토리 안에서 원하는 파일을 찾고자 할 때 사용하는 명령어**
* find [검색시작위치] [옵션1] [인자값1] [옵션2] [인자값2] ...

# 옵션
* -name  : 파일 이름을 기준으로 검색
* -iname : 대소문자를 구별하지 않고 검색
* -perm  : 파일 권한을 기준으로 검색
* -type  : 파일의 종류를 기준으로 검색
[b 블록 파일] , [c 문자] , [d 디렉토리] , [f 파일] , [s 소켓]
* -size  : 파일의 크기를 기준으로 검색
[+n : n보다 크다] , [-n : n보다 작다] , [n : n이다] , [b : 512-byte] , [c : Bytes] , [k : Kilo Bytes] , [M : Mega Bytes] , [G : Giga Bytes]
* -user  : 사용자의 ID 기준 검색
* -group : 사용자 그룹 기준 검색
* -atime  : 특정 기간 이상 접근하지 않은 파일 검색
* -mtime  : 특정 기간 이상 수정되지 않은 파일 검색
* -inode  : number 지정된 inode 번호와 파일을 찾는다.
* -empty  : 빈 파일 찾기
* -exec command {} ₩; : 찾은 각 파일에 대해 지정된 명령을 실행
* -ok command {} \;   : 실행여부(실행 되어 있는지 아닌지)를 사용자에게 확인 후 명령을 실행


## 응용문제 확인
1. /test 디렉토리의 파일과 디렉토리 개수 확인하기

2. /test 디렉토리 내의 파일중에 용량이 1G이상으로서 team01 계정의 권한으로 설정되어 있는 파일을 검색하여 자동 삭제하기

3. /test 디렉토리의 파일에서 system 문자가 들어있는 모든 파일을 찾고 해당 파일들의 목록을 /training 디렉토리에 exam1로 저장한다.

4. /test 디렉토리에서 setUID가 설정되어 있는 파일 찾기

5. /test 디렉토리의 모든 디렉토리의 소유자를 team33 그룹소유자를 team50으로 변경

> 해당 작업을 하면서 출력 로그를 확인하면서 바로 저장한다. + 에러결과를 따로 저장한다.
6./test 디렉토리의 모든 파일을 소유자 team02 그룹소유자를 team50으로 변경
> 해당 작업을 하면서 출력 로그를 확인하면서 바로 저장한다. + 에러 결과를 같이 저장한다.

7.

8. /goott 디렉토리에 team001계정이 접근하여 읽기 쓰기가 가능하도록 소유자 정보가 아닌 권한정보를 변경하기

9. 쓸데없이 용량을 사용중인 디렉토리 찾기

## 정답
1. /test 디렉토리의 파일과 디렉토리 개수 확인하기
```console
find /test -type f -o -type d | wc -l
```

2. /test 디렉토리 내의 파일중에 용량이 1G이상으로서 team01 계정의 권한으로 설정되어 있는 파일을 검색하여 자동 삭제하기
```console
find /test -size +1G -user team01 -type f  -exec rm -fv {} \;
```
3. /test 디렉토리의 파일에서 system 문자가 들어있는 모든 파일을 찾고 해당 파일들의 목록을 /training 디렉토리에 exam1로 저장한다.
```console
grep -lr system /test > /training/exam1
```
참고 
```console
find /test -type f -exec grep -l system {} \;
```
4. /test 디렉토리에서 setUID가 설정되어 있는 파일 찾기
```console
find /test -perm -4000 -type f -exec ls -l {} \;
```
5. /test 디렉토리의 모든 디렉토리의 소유자를 team33 그룹소유자를 team50으로 변경
```console
find /test -type d -exec chown -v team33:team50 {} \;
```
> 해당 작업을 하면서 출력 로그를 확인하면서 바로 저장한다. + 에러결과를 따로 저장한다.
```console
find /test -type d -exec chown -v team33:team50 {} \; 2> /root/errlog.log | tee -a /root/chowncheck.log
```

6./test 디렉토리의 모든 파일을 소유자 team02 그룹소유자를 team50으로 변경
> 해당 작업을 하면서 출력 로그를 확인하면서 바로 저장한다. + 에러 결과를 같이 저장한다.
```console
find /test -type f -exec chown -v team02:team50 {} \; 2>&1 | tee -a /root/filechown.log
```
7.

8. /goott 디렉토리에 team001계정이 접근하여 읽기 쓰기가 가능하도록 소유자 정보가 아닌 권한정보를 변경하기
```console
mkdir /goott
[root@nohost test]# cat /etc/passwd | grep team001
[root@nohost test]# useradd team001
[root@nohost test]# chmod 777 /goott/
[root@nohost test]# ls -ld /goott/
drwxrwxrwx. 2 root root 6 Sep 14 20:56 /goott/
```

9. 쓸데없이 용량을 사용중인 디렉토리 찾기
```console
du -sk /* | sort
[root@nohost ~]# du -k 
4	./.cache/dconf
4	./.cache
4	./.dbus/session-bus
4	./.dbus
0	./.config/ibus/bus
0	./.config/ibus
60	./.config/pulse
60	./.config
4	./bin
1082392	./.hidden
1129176	.
[root@nohost ~]# rm -rf .hidden/
```