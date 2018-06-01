
# PoS 의 스테이킹

보유 지분들의(UTXO) 고정 정보와 시간이란 유동 정보를 바탕으로 nHashInterval 간격으로 허용된 범위(nHashDrift, 기본 45초 , 최대 3분)
를 검증(PoW 방식과 동일)하여 검증정보와 검증에 사용된 스테이커 지분(staking utxo) 정보를 블록 생성시 사용하는 방식.
스테이커 지분을 스펜딩하여 블록정보와 함께 타 노드가 지분증명을 검증할 수 있도록 함.

스테이킹 노드 전체가 보유 지분들로 허용된 범위에서 PoW 를 수행하나, 증명을 못할 경우 시간의 흐름과 함께 자동으로 검색 범위가 넓어짐.
블록 주기(1분)에 맞춰 DarkGravity v3 (from Dash) 로 난이도가 매 블록마다 조정됨.

# nHashInterval

지분증명시 실패 후 재 검색용 새로운 Hash 를 위해 기다리는 시간. 기본 22초 (아주 잘못된 설정임)
마지막 검색과 현재의 블록높이가 다를 경우 zero delay
가능한한 짧은 시간 간격 (마지막 테스트는 2000ms 간격으로 Height 검사하고 최대 4초[즉 2회])
PoS 에서도 문제 있는 코드이긴 했으나, 전체 노드가 동일 퍼포먼스로 동작하기 때문에 문제가 덜 했다면,
zPoS 로 오면서 CPU 와 상황에 따라 블록생성 시간이 최대 분단위로 소요되면서 극악의 문제로 번짐.
그리고 zPoS 를 PoS 와 다르게 input stake 의 nTimeBlockFrom 와 nStakeModifier 가 고정이 아니라 변함.

# nHashDrift

45초가 기본이고 미래 시간을 탐색하며 최대 3분.
현재 테스트 노드에서 60초로 아주 잘 동작하고 있음.
과거 탐색 허용은 GetMedianTimePast 으로 제한되는데 정확한 내용은 추가 분석 필요.


# 스테이킹 시작 조건

getStakeStatus 설명
Piv zPiv 숙성 조건

# Hash 생성 조건

코드 보고 대충 적기

# 분석과제

## GetMedianTimePast

## zPiv stake 의 GetBlockTime 과 StakeModifier 변화

## kernel found 후 MintToTxin 까지의 딜레이

zerocoin speding 딜레이
securityLevel 과 함께 denom 생성 후 현재까지 모든 덩어리에서 랜덤 추출 한다고 들은것 같음.

