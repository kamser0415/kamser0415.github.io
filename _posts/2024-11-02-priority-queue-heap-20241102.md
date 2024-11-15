---
layout: single
title: 우선 순위 큐와 힙
categories: TIL
tag: [Today I Learned, Data Structure]
toc: true
toc_sticky: true
author_profile: false
sidebar:
    nav: "docs"
search: true
sidebar:
    nav: "counts"
typora-root-url: ../
---
목표 D-day : 32 일

2024-11-02 스카 출근 9시 47분 퇴근 미정

1차 TIL 우선 순위 큐와 힙(heap)

## 학습 목표

+ 힙의 정의를 이해한다.
+ 힙의 구성 방법을 이해하고, 힙의 노드 삽입 연산과 삭제 연산을 이해한다.
+ 최대힙과 최소힙을 이해한다.

### 용어 정리

+ 힙(heap): 부모 노드와 자식 노드 사이에 일정한 대소 관계를 정의하여 구성한 완전 이진 트리이며 우선순위 큐와 같음
+ 최대힙: 루트가 가장 큰 값을 갖고 부모는 자식보다 큰 값을 갖는 완전 이진 트리
+ 최소힙: 루트가 가장 작은 값을 갖고 부모는 자식보다 작은 값을 갖는 완전 이진트리



### 큐(Queue)

+ 선형 자료구조
+ 한쪽에서 삽입 연산만 가능하고, 다른 쪽에서 삭제 연산만 가능하다.

> 먼저 들어온 데이터가 먼저 처리되며, 선입 선출(FIFO) 구조를 가진다.

##### 큐 정리

선형 자료구조는 삽입과 삭제가 빠릅니다. 대신 특정 데이터 검색과 삭제가 오래 걸립니다.

한쪽에서 삭제하고 다른 쪽에서 삭제 연산만 가능하므로 순서가 중요한 자료구조가 필요할 때 사용됩니다

### 우선순위 큐(Priority Queue)

1. 삭제 함수를 실행하면 저장된 데이터 중 우선 순위가 제일 높은 것이 삭제된다.

2. 나머지 데이터는 어떤 순서대로 저장되어도 상관이 없다.

> 삭제 함수를 실행하면 삭제 위치는 동일하고 우선 순위 큐에서 우선 순위가 제일 높은 데이터가 맨 앞으로 이동합니다.
>
> **삭제 위치는 변경되지 않습니다.**

**자료구조**

> 추상화를 통해 알고리즘에서 사용할 자료의 논리적인 관계를 구조화한 것.
>
> 큐 자료 구조를 사용한다면 선입선출, 선형을 추상적으로 관계를 표현할 수 있는 표현



### 힙(heap)

우선순위 큐 자료 구조를 **트리 방식**으로 구현한 자료구조 중 하나입니다.

트리로 구현하면서 부모와 자식 노드 사이에 일정한 대소 관계를 유지하는 조건이 추가되었습니다.

##### 생각 정리

자료구조를 사용하면 하나의 단어로 자료의 논리적인 관계를 알 수 있습니다.

논리적인 관계를 배열의 인덱스로 표현할 수 있고, 노드의 포인트로 표현이 가능합니다.

배열로 저장하는 경우 인덱스를 어떻게 관리할 것인지

연결 리스트로 저장하는 경우 포인터가 어디를 가르킬지에 따라 다릅니다.

#### 트리로 구현한 이유

힙 자료 구조를 배열, 연결 리스트, 트리 등으로 구현할 수 있습니다.

배열로 구현했다면 `front`쪽에 항상 우선순위가 높은 데이터가 할당되어야합니다.

예를 들면, 숫자가 작은 것이 우선 순위가 높다고 할 때를 전제로 합니다.

데이터가 배열에 1,8,6,2,5 가 차례대로 저장되어있습니다.

우선 순위 큐에서는 맨 앞에만 우선 순위가 제일 높으면 됩니다.

8,6,2,5에서 제일 작은 숫자가 와야합니다.

배열은 여기서 제일 작은 숫자를 확인하기 위해 모든 데이터를 순회 해야 합니다.

그리고 2가 맨 앞으로 이동하게 되면 그 뒤에 있는 숫자는 한칸 씩 앞으로 이동 해야 합니다.

데이터 저장할 때는 맨 앞에 데이터만 확인하면 됩니다.

데이터를 저장하고, 삭제하고 할때마다 배열내 데이터가 복사이동하는 과정이 생기므로 비효율적입니다.

그리고 삭제할 때마다 우선 순위를 찾아야하므로 찾는 조회시간도 필요하게 됩니다.

연결 리스트로 구현한다면 배열보다 삭제나 추가시 필요한 리소스는 적게 들지만, 마찬가지로 다음 우선순위를 찾을 때 시간이 많이 필요합니다.

**트리**

트리 구조는 데이터 검색 시 logN 속도로 빠르게 찾을 수 있습니다.

그리고 차수를 고정하면 인덱스를 활용할 수 있고, 완전 이진 트리로 만든다면 배열의 크기도 효율적으로 사용이 가능합니다.

완전 이진 트리를 배열로 만든다면 왼쪽부터 차근차근 채워가기 때문에 배열의 크기도 논리적인 크기와 물리적인 크기가 동일하기 때문입니다.

#### 힙의 구성 방법

1. 완전 이진 트리 - 기억장소 낭비가 없으며 배열로 구현하는 경우 인덱스로 빠른 접근이 가능합니다.

   트리 마지막 노드만 예외적으로 비워져 있을 수 있습니다.

2. 힙 속성

   최대 힙: 각 부모의 노드는 자식의 노드보다 큽니다, 루트 노드에는 트리의 최댓값이 위치합니다.

   최소 힙: 각 부모 노드는 자식 노드보다 작습니다. 따라서 루트 노듣에는 최솟값이 위치합니다.



### 힙을 언제 쓰면 좋을까?

**우선순위를 유지하는 트리 구조**방식이므로 다음과 같은 상황에 주로 사용합니다.

우선 순위( 제일 크다, 제일 낮다)를 기준으로 데이터를 가져와서 사용하는 경우에 적합합니다.

1. 최댓값/ 최소값을 빠르게 찾아야할때
2. K번재 최댓값/최솟값을 찾을 때

3. 스케줄링등