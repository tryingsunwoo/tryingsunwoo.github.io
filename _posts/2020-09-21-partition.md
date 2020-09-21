---
title: "partition"
excerpt: "파티션 작업"
toc: true
toc_sticky: true
categories:
- linux
tags:
- linux
last_modified_at: 2020-09-21

---
## 파티션 (Particion)
### 파티션이란?
* 컴퓨터에서 디스크나 메모리 등의 저장 매체를 사용하고자 하는 영역만큼 나누는 것을 말한다.
 하드 디스크 드라이브처럼 용량이 비교적 큰 저장 매체를 유용하게 쓰기 위해 고안되었다. 즉, 하드 디스크 드라이브에 새로운 프로그램을 계속 설치하면 디렉토리로 관리하는 데 한계가 생기며, 컴퓨터 바이러스에 감염될 경우 모든 프로그램과 데이터들을 사용하지 못하게 될 수도 있다. 이에 대비하여 하드 디스크드라이브를 나눠 놓으면 프로그램을 별도로 저장할 수 있으며, 컴퓨터바이러스에 감염되더라도 그 부분만 제거할 수가 있다.

#### 파티션을 나누는 이유?
 1. 루트 파티션 파괴시 다중 파티션 자료보호 & 자료 백업 및 업그레이드 수월
 2. 다중 운영체제 설치시 다중 파티션 필요
 3. 파일 시스템 점검 시간을 줄여 부팅시간 감소

![](https://github.com/tryingsunwoo/tryingsunwoo.github.io/blob/master/assets/image/partition/Partition.PNG?raw=true)
* 하나의 하드디스크로 만들 수 있는 주 파티션의 개수 = 최대 4개
* 이러한 제약을 극복하기 위해서 확장 파티션과 논리 파티션이 등장했다.
* 주 파티션 4개에서 더 많은 파티션이 필요한 경우 네개중 하나를 확장파티션으로 하여 그 안에 논리 파티션을 가질 수 있음.

### fdisk 를 통한 파티션 구성과 삭제
```console
# fdisk -l         (# fdisk -l | grep Disk)
# fdisk –l /dev/sda 
```
fdisk -l 명령어를 통해 파티션 정보를 확인할 수 있음.

```console
# fdisk /dev/sda
```
fdisk 를 통해 새로운 파티션 생성이나 제거, 파티션의 타입결정등의 작업을 할 수있음.
fdisk 명령어는 2TB 이상의 디스크에서는 파티션 설정 할 수 없음. 그럴 때에는 parted 명령어 사용해야한다.
```console
# fdisk /dev/sdb
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel. Changes will remain in memory only,
until you decide to write them. After that, of course, the previous
content won''t be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

command (m for help) : m                 /* fdisk command menu의 정보가 보인다*/
Command action
   a   toggle a bootable flag              /* 부트 가능한 플래그로 변경 (부트 파티션 저장) */
   b   edit bsd disklabel                  /* bsd 디스크 레이블을 편집 */
   c   toggle the dos compatibility flag   /* 도스 호환 플래그로 변경 */
   d   delete a partition                 /* 파티션 삭제 */
   l   list known partition types        /* 알려진 파티션 형태의 목록 (지원 가능한 파티션 보기) */
   m   print this menu                    /* 메뉴를 출력 */
   n   add a new partition                 /* 새로운 파티션 추가 */
   o   create a new empty DOS partition table /* 새로운 도스 파티션 테이블을 생성 */
   p   print the partition table         /* 파티션 테이블을 출력 (파티션 설정 상태 확인) */
   q   quit without saving changes     /* 변경을 저장하지 않고 종료 */
   s   create a new empty Sun disklabel  /* 새로운 Sun 디스크 레이블을 생성 */
   t   change a partition's system id    /* 파티션의 시스템 id를 변경 (파일 시스템 유형 변경) */
   u   change display/entry units          /* 표시/엔트리 단위를 변경 */
   v   verify the partition table         /* 파티션 테이블을 점검 */
   w   write table to disk and exit      /* 디스크에 테이블을 기록하고 빠져나감(파티션 정보 저장) */
   x   extra functionality (experts only)
   ```
n을 입력하면 새로운 파티션을 주파티션으로 구성할지 확장파티션으로 구성할지를 선택 가능
```console
[root@sevrer3 ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-4194303, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-4194303, default 4194303):

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): p
Disk /dev/sdb: 2 GiB, 2147483648 bytes, 4194304 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x75dbbff3

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 4194303 4192256   2G 83 Linux

Command (m for help): d
Selected partition 1
Partition 1 has been deleted.

Command (m for help): p
Disk /dev/sdb: 2 GiB, 2147483648 bytes, 4194304 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x75dbbff3
```
* 새로운 파티션을 구성 후 확인하고 삭제 후 다시 확인함




