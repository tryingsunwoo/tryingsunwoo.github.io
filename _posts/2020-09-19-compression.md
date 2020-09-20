---
title: "compression and archive"
excerpt: "압축과 아카이브의 차이"
toc: true
toc_sticky: true
categories:
- linux
tags:
- linux
last_modified_at: 2020-09-19
---
## 리눅스 확장자별 압축
윈도우에서 파일을 압축 한다는 것의 의미는 여러 파일들을 하나로 묶고, 용량을 압축해 주는 것을 뜻했다.
하지만 리눅스는 파일 '압축'과 '묶기' 가 별개의 개념이고 명령어도 서로 다르기 때문에 포스팅하였다.

### gzip / gunzip 명령어
* 압축
gzip 명령어는 파일을 압축할 때 사용하며 파일에 확장자 (.gz)가 붙게된다.
```console
# gzip file1
```
#### 옵션
* -S : 기본 생성 파일인 .gz 형태가 아닌 지정한 파일로 생성한다.
* -d : 압축된 파일의 압축을 해제한다. 이는 uncompress명령과도 같다.
* -f : 이미 이전에 압축파일이 존재하더라도, 무시하고 압축파일을 생성한다.
* -r : 지정한 것이 디렉토리라면 하위 디렉토리와 파일까지 모두 압축한다.
* -v : 압축 통계를 보여준다.
* -[1-9] : 압축 레벨 지정


* 압축파일 내용 확인
```console
# zcat file1.gz
```
* 압축 해제
```console
# gunzip file1.gz
```


### tar 명령어
tar 커맨드는 실상 파일을 압축을 하지 않는다. 묶는다는 개념
#### 옵션
* -c : 여러 개의 파일을 하나의 파일로 묶을 때
* -v : 생성과정을 보여 줌
* -x : 파일의 압축을 해제하고 풀어 낼 때
* -f : 기본장치인 테이프가 아니라 파일로서 동작시키는 경우 사용되는 옵션
* -r : 파일 및 디렉토리 추가
* -u : tar 파일과 새로운 디렉토리 내의 파일과 비교하여 최근 파일을 아카이브에 추가
* -t : tar 파일의 내용을 확인할 때
* -z : gzip과 관련하여 압축이나 해제를 한꺼번에 할 때
* -j, -l : bzip2를 이용해서 압축한다.

* 아카이빙
```console
# tar cvf <묶음파일명> <파일명1> <파일명2>
```
 : create, v: verbose, f: file or tape
* 파일 내역확인
```console
tar tvf <묶음파일명>
```
: t: content 
 * 아카이빙 파일 해제
 ```console
# tar xvf <묶음파일명>
 ```
x: extract

## tar.gz로 압축하기
* **합쳐진 tar파일을 압축하는 방식으로 좋은 압축옵션임**
```console
# tar zcvf <묶음파일명.tar.gz> 폴더명
```
## tar.gz 압축풀기
```console
# tar zxvf <묶음파일명.tar.gz>
```
