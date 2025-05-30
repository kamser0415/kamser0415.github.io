---
layout: single
title: TIL) 스레드풀 확장과 Blocking I/O의 한계 인식
categories: Reactive-Learning
tag: [blocking-io, threadpool, server-performance]
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

오늘은 리액티브 스트림에 대해서 추가로 학습을 하며 자바 환경에서 리액티브 스트림을 사용한다는 의미를 알아가려고 합니다.

### **🚨 문제 1**



**동기 I/O가 효율적인 서비스 환경을 네가 생각하는 대로 구체적인 조건을 붙여서 설명해봐.**



- I/O 크기

- 요청 빈도

- 네트워크 품질

- 비즈니스 요구 시간

- 스레드/CPU 관점

  



------





### **🚨 문제 2**





**동기 I/O가 치명적으로 비효율적인 서비스 환경을 네 관점에서 사례로 들어봐.**



- 서비스 예시 2가지
- 불리한 이유 2가지 이상
- 그 이유가 왜 네트워크 I/O가 병목인지





------





### **🚨 문제 3 (심화 유도)**





**동기 I/O의 안정성을 일부러 선택하는 실무 사례를 기업 서비스 관점에서 들어봐.**



- 왜 일부러 비효율을 감수하고도 동기를 고집하는지
- 비용, 유지보수, 장애 대응, 일관성 측면을 꼭 언급해줘.





------





### **🚨 문제 4 (고급 반전 질문)**





**네트워크 I/O 응답이 빠른 환경에서도 굳이 비동기를 안 쓰고 동기를 쓰는 게 더 나은 이유는 무엇이라고 생각하나?**



- 비동기의 ‘숨겨진 비용’을 네 관점에서 최소 2가지 이야기해봐.
