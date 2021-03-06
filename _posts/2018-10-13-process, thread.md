---
layout: post
title: "Process,Thread"
date: 2018-10-13 00:53:14 +0100
categories: study
author: dongwon
location: Seoul, Korea
description: thread
tail: I'm a front developer
---

# 프로세스와 스레드의 차이점

예전부터 굉장히 헷갈렸던 개념이고 2 번이상씩 구글링을 통해서 검색해본것 같아서 차라리 나의 언어로 정의하는 것이 낫다고 생각하여 정리해본다.

일단 한국말로 정리해보자면

> 프로세스는 운영체제로부터 자원을 할당받는 작업의 단위이고 스레드는 프로세스가 할당받은 자원을 이용하는 실행의 단위이다.

이렇게 정의할 수 있다.

프로세스는 실행될 때 운영체제로 부터 프로세스를 할당받고, 운영되기 위해 필요한 주소 공간, 메모리 등 자원을 할당받는다.

스레드란 프로세스 내에서 동작되는 여러 실행의 흐름으로, 프로세스 내의 주소 공간이나 자원들을 같은 프로세스 내에 스레드끼리 공유하면서 실행된다.

=> 고로 스레드와 프로세스에 대한 질문은 운영체제가 시스템의 자원을 어떤 단위로 할당하고, 프로세스와 스레드는 이 자원을 어떻게 사용하느냐를 알고 있냐는 질문이다.

그렇다면 왜 어떤 프로세스로 할 수 있는 작업들을 굳이 하나의 프로세스에서 스레드로 나눠가지고 할까?

운영체제는 시스템 작업을 효율적으로 관리하기 위한 것이므로 이 목적을 달성하기 위해 스레드를 사용하는 것이다. 자세히 말하자면 멀티 프로세스로 실행되는 작업을 멀티 스레드로 실행할 경우 프로세스를 생성하여 자원을 할당하는 시스템 콜이 줄어들어 자원을 효율적으로 관리할 수 있다. 뿐만 아니라 프로세스 간의 통신보다 스레드 간의 통신의 비용이 적으므로 작업들 간의 통신의 부담이 줄어든다.

이처럼 스레드를 사용하면 자원의 효율성이 증가하지만 스레드 간의 자원공유는 전역 변수를 이용하므로 동기화 문제에 신경을 써야한다. 때문에 멀티스레드 프로그래밍은 주의를 요한다.

> 프로세스와 스레드에 대해서 설명해주세요

이 질문의 포인트는 운영체제에서 작업을 실행할 때 자원을 할당하는 단위를 알고 있느냐와 프로그램을 멀티 스레드를 구현할 때 장, 단점을 알고 있느냐에 대한 질문이다.

## Linux 관점에서

리눅스 등의 멀티 프로세싱 운영체제에서는 동시에 여러 프로그램을 실행할 수 있다.

그때 실행중인 프로그램에 대한 인스턴스를 프로세스라고 한다.

프로세스는 프로그램에 대한 각각의 인스턴스를 의미하므로, 같은 프로그램을 여러개 띄웠다고 해서 하나의 프로세스를 공유하는 것은 아니다. 프로세스는 운영체제로부터 주소공간, 파일, 메모리 등을 할당받는다.

리눅스 시스템에서는 코드 영역과 라이브러리를 프로세스 간에 공유하므로, 메모리 내에 코드와 라이브러리는 하나만 존재한다. 변수에 할당되는 공간으로 데이터 세그먼트와 스택 세그먼트는 프로세스 각각 가지고 있다. 그리고 각 프로세스마다 실행 위치를 나타내는 PC 도 프로세스별로 관리한다.

스레드란 한 프로세스 내에서 동작되는 여러 실행의 흐름으로, 프로세스 내의 주소 공간이나 자원들을 대부분 공유하면서 실행된다.

기본적으로 하나의 프로세스가 생성되면 하나의 스레드가 같이 생성된다. 이를 `메인 스레드`라고 부르며, 스레드를 추가로 생성하지 않는 한 모든 프로그램 코드는 메인 스레드에서 실행된다. 또한 프로세스는 여러개의 스레드를 가질 수 있으며 이를 `멀티 스레드`라고 한다.

> 프로세스는 자신만의 고유 공간과 자원을 할당받아 사용하는데 비해 스레드는 다른 스레드와 공간과 자원을 공유하여 사용한다.

스레드는 프로세스 내에서 각각의 스택 공간을 제외한 나머지 공간과 시스템 자원을 공유한다. 그러므로 프로세스를 이용하여 동시에 처리하던 일을 스레드로 구현할 경우 메모리 공간은 물론 시스템 자원 소모도 현격히 줄어든다. 이와 같이 프로세스를 생성하는 것보다 스레드를 생성하는 것이 효율적이다. 특히 멀티 프로세서 환경에서는 더욱 탁월하다. 스레드 간의 통신이 필요한 경우 별도의 자원을 이용하는 것이 아니라 전역 변수의 공간을 이용하여 데이터를 주고 받을 수 있다.

스레드의 장점을 정리하면 다음과 같다.

- 시스템의 throughput 이 향상된다.
- 시스템의 자원소모가 줄어든다.
- 프로그램의 응답 시간이 단축된다.
- 프로세스 간 통신 방법에 비해 스레드 간의 통신방법이 훨씬 간단하다.

마지막에 언급한 스레드 간의 통신시 데이터를 주고받는 방법은 메모리 공간을 공유하므로 데이터 세그먼트, 즉 전역 변수를 이용하여 구현한다. 그런데 여러 스레드가 함께 사용하면 충돌이 나는데 이를 해결하기 위해 동기화 문제를 해결해야 한다.

스레드는 장점뿐 아니라 단점도 가지고 있는데

- 여러 개의 스레드를 이용하는 경우 주의깊게 설계해야한다. 미묘한 시간 차나 잘못된 변수를 공유함으로 오류가 발생할 수 있다.
- 프로그램 디버깅이 어렵다
- 단일 프로세서 시스템에서는 효과를 기대하기 어렵다.

## 참고 문헌

[강관우님 브런치](https://brunch.co.kr/@kd4/3)

[진형아빠님 tistory](http://ralf79.tistory.com/34)
