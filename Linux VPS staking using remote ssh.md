
> 리눅스 VPS CLI 모드로 스테이킹을 운영함에 있어 원격지에 지갑 비밀번호 노출이 없는 환경을 구성한다.

# TOC
  1. iwinv 를 이용한 리눅스 VPS 구축
      > 또는 다른곳, 아마존 AWS는 CPU/IO 크레딧 체계로 인해 비추
     1. 가입
     1. 서버생성
     1. 기본설정 (기초 패키지, 시간 동기화 등)
     1. 보안설정 (포트포워딩)
  1. 리눅스용 지갑 설치
     1. 기본 링크 스크립트
     1. 기본 경로 설정 스크립트
     1. 블록체인 동기화 방법
        1. 무작정 기다리기
        1. 스냅샷을 통한 동기화
     1. 설정파일 변경 (~/.pivx/pivx.conf)
     1. 단독 사용 방법 간단히 설명
        1. 비밀번호
        1. 지갑 잠금/해제
        1. 잔액조회
        1. 입금 받기
        1. 송금 하기
     1. 지갑 시작/종료
  1. 기존 지갑파일 옮기기
     1. 반드시 길고 유추하기 어려운 비밀번호가 설정되어 있어야 한다
     1. 파일질라SSH 통해 복사 하기
     1. 지갑재시작
  1. 제어 PC 설정
     1. 파일질라 설치
     1. openSSH 설치
     1. powershell 설치
     1. 지갑 설치
  1. 제어 PC 의 powershell 을 통한 명령
     1. 지갑 잠금해제 walletpassphrase
     1. 잔액조회 getinfo
     1. 제로코인조회 getzerocoinbalance
     1. 스테이킹 상태 조회 getstakingstatus
  1. 리눅스 VPS 지갑과 PC 지갑 동기화 하기
     > 결정적 시드 지갑 설명
     1. dzpivstate
     1. searchdzpiv
     1. resetmintzerocoin



```
enablezeromint=0

rpcuser=this_is_rpcuser
rpcpassword=this_is_rpcpassword
rpcallowip=127.0.0.1

listen=1
server=1
daemon=1
logtimestamps=1
maxconnections=256

externalip=insert_vps_public_ip
```

```
./pivx-cli encryptwallet testpass

ssh root@49.247.205.35 "~/pivx-3.1.0/bin/pivx-cli getinfo"
ssh root@49.247.205.35 "~/pivx-3.1.0/bin/pivx-cli walletpassphrase testpass 9999999999 false"
ssh root@49.247.205.35 "~/pivx-3.1.0/bin/pivx-cli getzerocoinbalance"
ssh root@49.247.205.35 "~/pivx-3.1.0/bin/pivx-cli getstakingstatus"
```
