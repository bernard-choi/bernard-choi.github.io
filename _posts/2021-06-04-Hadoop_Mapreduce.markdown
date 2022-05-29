---  
layout: post  
title: "Hadoop Ecosystem - MapReduce"  
subtitle: "Hadoop Ecosystem - MapReduce"  
categories: Development
tags: Hadoop HDFS MapReduce Spark
comments: true  


---  

# Hadoop Ecosystem - MapReduce

## Hadoop 의 3대 요소

- [분산파일시스템(`HDFS`) - for 대용량](https://yunsikus.github.io/data/2021/02/22/Hadoop/)
- 분산병렬처리(`MapReduce`) - for 빠른계산
- 하둡클러스터 자원관리시스템(`YARN`) - 클러스터 자원 스케줄링

---

## MapReduce의 3대 원리

거대한 데이터를 쪼개서 개별 계산 ( `Map` )

데이터를 전송 ( `Shuffle` )

개별 계산된 데이터를 합산하여 최종 값을 도출 ( `Reduce` )

---

## 소계/총계 기법

![hdfs1](https://blog.kakaocdn.net/dn/Exrmg/btqK14SoSBu/UUOAwYbjW3pmbCoDg3x99k/img.png)


`(1) split` → `(2) map(사용자정의)` → `(3) Shuffle(데이터전송)` → `(4) Reduce(사용자정의)` → `(5) Output 저장`

## 평균의 경우?


![hdfs2](https://yunsikus.github.io/assets/img/post_img/Hadoop_Mapreduce2.jpg)

split을 어떻게 하냐에 따라서 맞을때도 있고 틀릴때도 있다. → 병렬처리 불가능

 But, SUM / N 을 저장한다.

- 1차 시도

```
(1, 1), (2, 1), (3, 1) → (6, 3)

(4, 1), (5, 1), (6, 1) → (15, 3)

(7, 1), (8, 1), (9, 1), (10, 1) → (34, 4)

→ (55, 10) → 5.5
```

- 2차 시도

```
(1, 1), (2, 1), (3, 1) → (6, 3)

(4, 1), (5, 1), (6, 1), (7, 1) → (22, 4)

(8, 1), (9, 1), (10, 1) → (27, 3)

→ (55, 10) → 5. 5
```

분산처리 알고리즘이 Spark에서 내부 함수로 모두 구현되어 있으며, 필요하면 직접 구현하면 됨.  

**소계/총계 기법의 소요시간 :  (기준 시간 / Map 개수) + a(데이터 이동)**

---

## Sort/Merge 기법

대용량 데이터 정렬의 경우 데이터가 증가할수록 소요시간이 급증함.

→   MapReduce의 효율이 소계/총계 보다 좋음


![hdfs3](https://yunsikus.github.io/assets/img/post_img/Hadoop_Mapreduce3.jpg)

Map 10개로 1억건을 정렬한다고 하자.


![hdfs4](https://yunsikus.github.io/assets/img/post_img/Hadoop_Mapreduce4.jpg)


![hdfs5](https://yunsikus.github.io/assets/img/post_img/Hadoop_Mapreduce5.jpg)

Map : 100분(10조각이 병렬처리되기 때문)

Shuffle 시간 : 1분 → 10분 (병렬처리가 아니라 순차적으로 처리)

Reduce 시간 : 1분 → 10분 (Merge-Sort 알고리즘)

120분 맵 10개를 써서

**Sort/Merge 기법의 소요시간 :  (기준 시간 / Map 개수\*\*2) + a(데이터 이동)**

---

## Sort/Merge + Group/By 기법

종류별로 나눠서 정렬하는 경우

맵10개 / 한 맵당 4개의 파티션으로 나눠지고 —> 각 4개 종류별로 정렬

![hdfs6](https://yunsikus.github.io/assets/img/post_img/Hadoop_Mapreduce6.jpg)

맵이 4개의 파티션으로 나눠짐. → Reduce도 4개의 파티션으로 나눠질 예정

Map 시간 : (100 / 4^2) * 4 = 25분 (파티션은 병렬 X)

Shuffle 시간 : 10분 / 4 = 2.5 분

Reduce시간 : 10분 / (4^2) * 4 = 2.5 분

**Sort/Merge + Group/By 기법의 소요시간 :  (기준 시간 / 파티션 개수) + a(데이터 이동)**

---

## Reference

[https://learning-sarah.tistory.com/entry/하둡-기본개념-MapReduce](https://learning-sarah.tistory.com/entry/%ED%95%98%EB%91%A1-%EA%B8%B0%EB%B3%B8%EA%B0%9C%EB%85%90-MapReduce)

에듀캐스트 빅데이터 Part1 하둡의 이해 강의
