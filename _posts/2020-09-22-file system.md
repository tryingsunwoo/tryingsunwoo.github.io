---
title: "file system"
excerpt: "linux file system"
toc: true
toc_sticky: true
categories:
- linux
tags:
- linux
- partition
last_modified_at: 2020-09-22
---
## 파일 시스템이란?
* 파일시스템(**File System**)이란 파일을 저장하고 관리하는 구조체계이다. 파일과 그 안의 자료를 저장하고 찾기 쉽도록 유지 관리하는 방법.

데이터를 저장하려면 우리는 물리적 장치 (디스크) 를 장착하고 디스크를 사용할 수 있도록 파티션 영역을 지정하는데, 사용하고자 하는 영역을 지정하고 그 안의 데이터 (파일과 자료)를 저장하고 관리할 수 있도록 파일 시스템을 만든다.

- 운영체제가 파티션을 인식할 수 있도록 파일시스템을 만들어야함.
- 기억 공간에 데이터를 저장하고 유지하기 쉽도록 도와주는 것이 파일 시스템이다.
- 파일 시스템은 운영체제에 의해서 만들어진 파일이다.
- 파일을 만들기 위해서는 파일 시스템이 만들어 진 곳에다가 파일을 만들어야 하기 때문에 이를 그냥 통칭해서 파일 시스템이라 부른다.

### 파일시스템 종류 확인

     **# export LANG=ko_KR.utf8**

     **# man 5 filesystems**

### 파일시스템 종류

    **ext2** 예전 리눅스가 사용하던 파일시스템이다.

    **ext3** 현재 **CentOS 5.X** 사용하는 파일시스템

    **ext4** 현재 **CentOS 6.X** 사용하는 파일시스템

    **xfs** 고성능 저널링 파일시스템

### 파일 시스템 구조

- 리눅스는 가상 파일 시스템(**Virtual File System**) 구조를 가진다.

       따라서 수많은 파일 시스템을 지원한다.

- 파일 시스템 구조 확인(파일시스템의 meta-data 확인)

     **# dumpe2fs /dev/sda1**

## mkfs

* 새로운 파일 시스템을 만드는 명령입니다. root 만 사용가능.
fdisk 명령어로 생성한 파티션은 각 파티션이 독립적으로 동작하기 때문에 각각의 파일시스템이 필요함.
fdisk 로 생성한 파티션은 공간은 확장되어 있지만 포맷이 되지않아서 OS가 없다고 생각하면 편하다.

**mkfs -t 타입 [옵션] 장치**
```console
# mkfs -t ext4 /dev/sda1
```
/dev/sda1 을 ext4 파일시스템으로 만든다는 의미

ex)
```console
# mkfs –t ext4 /dev/sdb1        (-t : File System Type)
# mkfs.ext4 /dev/sdb1

# mkfs -t ext3 /dev/sdb1
# mkfs.ext3 /dev/sdb1
```

옵션의 의미 
-c
 배드블록 체크 후에 파일시스템을 생성 합니다. 
-t
 파일시스템의 유형을 지정하는 옵션입니다. 뒤에 파일시스템 유형을 기재하는데 지정하지 않으면 ext2유형으로 생성됩니다. 
-v
 상세히 출력합니다.

### minifree 공간 만들기
* minfree(Minimum Free Space) : 파일시스템(EX: ext3)이 풀(Full) 났을 때 관리자만 쓸수 있는 공간으로 예약된 공간(0% ~ 50%, Default : 5%)

최소 남은 공간(Mininum Free Space)는 전체 공간의 퍼센트로 할당된다. 이 공간은 파일시스템이 만들어질 당시에 예약되서 생성된다. 기본 예약 값은 ((64 MB / 파티션 크기) * 100) 이다. 이 값은 0%에서 50% 사이 값이 할당이 되고, 근사값으로 결정이 된다.

최소 남은 공간은 파일시스템이 Full 되었을 때 파일시스템을 접근 할 수 있는 최소로 공간으로 남겨져 있어야 하기 때문에 중요하다. 파일시스템이 Full 되면, root 사용자만 사용할 수 있는 공간으로 예약되어진 남은 공간이다.

Minfree 공간은 하나의 파티션에 0 ~ 50% 사이 값으로 정의된다. Minfree 공간은 파티션이 풀(Full)난 경우 root 사용자가 사용하기 위한 공간으로 사용된다. 일반사용자는 이 공간을 사용할 수 없다. 파일시스템에서 Minfree 공간은 0(zero)일수 없다. 최소 minfree는 0%이다.

```console
# mkfs.ext4 -m 10 /dev/sdb1
```