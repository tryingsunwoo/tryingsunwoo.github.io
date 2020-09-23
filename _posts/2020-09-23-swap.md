---
title: "linux swap"
excerpt: "swap 이란?"
toc: true
toc_sticky: true
categories:
- linux
tags:
- linux
last_modified_at: 2020-09-23

---
## SWAP 이란?
* 간단하게 말하자면 **스왑(SWAP) 파티션이란 컴퓨터 메모리(RAM) 이 넘칠 때 댐의 역할을 한다.**
메모리가 가득 차면 추가적으로 실행되는 프로그램은 메모리 대신 스왑 파티션에서 실행됨.
* 메모리 공간이 가득 찼을 때 프로그램 실행이 가능하도록 하는 **예비공간**임.

스왑 파티션의 장점
1. 메모리가 가득 찼을 때 보조 공간을 제공
2. 빠른 속도의 메모리에서 더 많은 공간을 사용할 수 있게 덜 필요한 항목을 옮길 수 있게 한다.
3. 최대 절전 기능을 사용할 수 있게한다.

스왑 파티션의 단점
1. 스왑 파티션은 동적으로 크기를 조정할 수 없어 하드디스크의 공간을 차지한다.
2. 하드디스크의 소모율을 높일 수 있다.
3. 반드시 성능을 향샹시켜주는 것은 아니다. (병목 현상이 나타날 수 있음.)

### SWAP 추가
- 스왑 추가방법에는 SWAP File 형태로 추가하는 방법과 SWAP PARTITION 형태로 추가하는 방법이 있음
#### 현재 스왑 공간 확인
```console
[root@server1 ~]# swapon -s
Filename                                Type            Size    Used    Priority
/dev/dm-1                               partition       2138108 0       -2
```
* 스왑이 잡혀있다면 결과가 나오지만 스왑이 잡혀 있지 않다면 아무런 결과가 출력되지 않는다.
* 가장 오른쪽에서 우선순위도 확인 가능
```console
[root@server1 ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:            952         365         264           7         322         424
Swap:          2087           0        2087
```
* #free -m 을 통해서도 확인 가능

#### File 형태로 추가
```console
# dd if=/dev/zero of=/swap/swapfile bs=1M count=10240 /* dd명령을 이용해서 swapfile이라는 스왑 파일을 만든다. */
# mkswap /swap/swapfile /* 스왑 영역 지정 및 스왑 활성화(SWAP ON) */        
# swapon /swap/swapfile /* 등록 */
# vi /etc/fstab /* fstab 파일에 등록 부팅시에 swap 이 추가 될 수 있도록 하는 것 아래 참고*/
```

/etc/fstab 파일에 등록
```console
# vi /etc/fstab 
..... (중략) .....
LABEL=SWAP-sda2         swap                    swap    defaults        0 0
..... (중략) .....
#
# SWAP Configuration
#
/swap/swapfile          swap                    swap    defaults        0 0  <----- 마지막 라인에 추가
```


#### Partition 형태로 추가
```console
# fdisk /dev/sdb (System ID : 82)       /* 파티션 생성 후 command t(파티션 타입 변경) HexCode 82 (스왑 파티션) */
# mkswap /dev/sdb1
# swapon /dev/sdb1
# vi /etc/fstab /* 스왑 파티션이 부팅시에 추가 될 수 있도록 fstab 에 추가하는 것 아래 참고 */
```

* fstab에 스왑파티션 추가
```console
# vi /etc/fstab 
..... (중략) .....
#
# SWAP Configuration
#
#/swap/swapfile          swap            swap    defaults        0 0
#/dev/sdb1               swap            swap    defaults        0 0 <----- 마지막 라인에 추가
```


### SWAP 삭제
#### SWAP File 형태 삭제
```console
# swapoff /swap/swapfile    /* 스왑 비활성화 */
# vi /etc/fstab /* fstab 에 등록했었다면 등록했던 것 삭제 */

# rm /swap/swapfile     /* 스왑파일 삭제 */
-> swap/swapfile을 SWAP ON 되었을때 자동으로 삭제되는 파일이 아니기 때문에 별도로 삭제해야 함.
```
#### SWAP Partition 형태 삭제
```console
# swapoff /dev/sdb1 /* 스왑 비활성화 */
# vi /etc/fstab /*fstab에 등록해뒀다면 등록했던 것 삭제 */

#fdisk /dev/sdb1로 해당 파티션을 삭제해주면 된다.
```