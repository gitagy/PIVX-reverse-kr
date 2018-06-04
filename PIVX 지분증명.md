
# PIVX 의 PoS

보유 지분들의(UTXO) 고정정보와 시간에 따른 유동정보를 바탕으로 nHashInterval 와 nHashDrift 를 제약과 범위로 하여
고정+유동 정보로 Hash 를 생성하여 PoW 를 수행한다.

Hit 된 Hash 에 사용된 검증정보를 바탕으로 블록을 생성하면서 사용된 스테이크 지분(UTXO)을 스펜딩하여 Peer 노드가 블록을 받아서 지분증명 유효성을 검증할 수 있는 방식.

스테이킹 노드 전체가 보유 지분들로 허용된 범위에서 PoW 를 수행하나, 증명을 못할 경우 시간의 흐름과 함께 자동으로 검색 범위가 넓어진다.

설정된 블록 주기인 1분에 맞춰 DarkGravity v3 (from dash) 방식으로 난이도가 매 블록마다 재 설정되어 평균적으로 1분 블록 주기를 맞출 수 있다.

# nHashInterval

지분증명 실패 시 재 탐색용 Hash 를 위해 대기하는 시간, 코어노드 22초, 최대 제한 없음

현재 내 테스트 노드에서는 현재/마지막 블록높이가 다를 경우 zero delay,
그리고 최대한 짧은 간격을 위해 블록높이 변화 검사 2000ms, 같은 블록높이 더라도 새로운 nTimeTx 적용을 위해 최대 4000ms 만 대기 (즉, 최대 2회 Loop 대기)

PoS 에서는 전체 노드가 동일 퍼포먼스(코어노드) 이기 때문에 문제가 작았다면,
zPoS 에서는 CPU 와 상황에 따라 블록생성 시간이 최대 분단위로 소모되면서 극악의 문제가 됨.

그리고 zPoS 는 PoS 와 다르게 input stake 의 블록시간(nTimeBlockFrom) 과 변경자(nStakeModifier) 가 불규칙적으로 변해서 PoS 때 만든 기준이 적합하지 않음.

# nHashDrift

Hash 탐색시 미래의 nTimeTx 범위를 정하는 기준.

코어노드 45초, 최대 3분

현재 내 테스트 노드에서 60초가 문제 없이 동작중.

# GetMedianTimePast

Hash 탐색시 미래 시간 범위는 nHashDrift 로 정의되고 과거 시간 범위는 GetMedianTimePast 로 제한됨.

값에 대해서는 추가 분석 필요. (spacing 이 11을 본것 같음)

# 스테이킹 시작 조건

* validtime 노드간 시간 유효?
* haveconnections 타 노드와 연결 되어 있나?
* walletunlocked 스펜딩을 위해 지갑이 언락되어 있나?
* mintablecoins 숙성이 끝난 코인이 있나?
* enoughcoins
* mnsync 마스터노드와 동기화가 끝났나?
* staking status 위의 조건을 모두 만족 하는가?

# 숙성 조건

## piv

```
unsigned int nStakeMinAge = 60 * 60;
```

## zPiv

```
nZerocoinRequiredStakeDepth = 200; //The required confirmations for a zpiv to be stakable
```

# Hash 생성

```
CDataStream ss(SER_GETHASH, 0);
ss << nStakeModifier << nTimeBlockFrom << ssUniqueID << nTimeTx;
hashProofOfStake = Hash(ss.begin(), ss.end());
```

# 분석과제

## GetMedianTimePast

```
enum { nMedianTimeSpan = 11 };

int64_t GetMedianTimePast() const
{
    int64_t pmedian[nMedianTimeSpan];
    int64_t* pbegin = &pmedian[nMedianTimeSpan];
    int64_t* pend = &pmedian[nMedianTimeSpan];

    const CBlockIndex* pindex = this;
    for (int i = 0; i < nMedianTimeSpan && pindex; i++, pindex = pindex->pprev)
        *(--pbegin) = pindex->GetBlockTime();

    std::sort(pbegin, pend);
    return pbegin[(pend - pbegin) / 2];
}
```

## zPiv stake 의 GetBlockTime 과 StakeModifier 변화

The nStakeModifier for the zpiv stake changes less often since it is based on the first accumulator checkpoint that occurs 60 minutes after pindexFrom.

Accumulator checkpoints change maximum of every 10 blocks.
sometimes it can be an even longer span than that too.

## kernel found 후 MintToTxin 까지의 딜레이

zerocoin speding 딜레이

securityLevel 과 함께 denom 생성 후 현재까지 모든 덩어리에서 랜덤 추출 한다고 들은것 같음.
