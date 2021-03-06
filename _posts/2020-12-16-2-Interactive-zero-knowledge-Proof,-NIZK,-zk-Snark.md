---
layout: post
title:   ZKP 기술보고서 - 2. 대화형(Interactive) 영지식 증명, NIZK, 그리고 zk-SNARK
date:   2020-12-16 00:00:01 +09:00
author: "장재혁"
categories: zkp
tag: [zkp]
youtubeId :
slideWebId :
---


> 작성자: 장재혁, [GIST 블록체인 인터넷 경제 연구센터 (센터장 이흥노)](https://infonet.gist.ac.kr/?page_id=6711)
>
> This work was created through a joint research with Onther Co., LTD., and supported by a grant-in-aid of Institute of Information & Communications Technology Planning & Evaluation (IITP), Republic of Korea.
>
> 이 글은 정보통신기획평가원(IITP)의 지원을 받아 (주)온더와의 공동연구를 통해 만들어진 결과물이다.

zk-SNARK는 zero-knowledge succint non-interactive argument of knowledge의 약자이다. zk-SNARK는 이전에 정의된 일반적인 영지 증명 프로토콜의 세 가지의 필요충분조건에서 "간결성(succintness)"과 "비대화형(no interaction)"의 조건이 추가된 발전된 형태의 영지식 증명이다.

영지식 증명의 succintness는 증거의 간결함, 즉 증명과정에 의해 산출되는 증거 데이터의 물리적 크기가 작음을 의미한다. 정확한 설명은 아니지만, 일반적으로 증거의 크기는 증명해야 할 명제의 복잡도에 따라 커진다. 예를 들어 어떤 함수 $$f$$와 값 $$x$$에 대해 증명해야 할 명제가 "$$f\left( x \right)=1$$" 인 경우, $$f$$의 계산 복잡도가 크면 증거의 크기도 커질 수 있다. 연구결과에 따라 다르지만, 간결성이 추가된 영지식 증명 프로토콜이 지향하는 것은 증거의 크기가 명제의 복잡도에 관계 없이 고정되어 있거나, 혹은 명제의 크기가 명제의 복잡도와 선형적인 관계 혹은 그 이하에 있는 프로토콜이다.

증거의 크기가 간결할 때의 이점은 검증자가 검증을 빠르게 끝낼 수 있다는 것이다. 증거의 크기가 작기 때문에, 검증자가 증거의 오류를 검사하기위해 소요해야하는 필요 계산량도 작아지게 된다. 이 뿐만 아니라, 증거를 게시해야 할 서버의 용량이 한정 된 경우에도 유용하다. 그러한 서버의 예가 블록체인인데, 블록의 크기가 제한적이기 때문이다. 따라서 증거의 크기가 크면 블록에 기록될 수 있는 증거의 수가 줄어든다. 이는 시간당 블록체인을 사용할수 있는 사용자의 수가 줄어드는것과 같다. 즉, 블록체인의 확장성(scalibility)이 줄어드는 것이다. 반대로, 증거의 크기가 작아질수록 같은 시간동안 더 많은 사용자가 블록체인을 사용 할 수 있게 되며, 따라서 블록체인의 확장성이 커진다.

비대화형 조건을 다루기 전에 먼저 고전적 형태인 대화형 (interactive) 영지식 증명에 관해 알아볼 필요가 있다. 대화형 영지식 증명의 특징은 증명자와 검증자가 함께 증거를 만드는 것이다. 먼저 증명자가 증거의 일부를 생성하고, 검증자가 증명자에게 도전적 문제 (challenge)를 제출한다. 도전적 문제란 증명자는 비공개 정보를 알지 못하면 응할 수 없는 문제를 말한다. 최종적으로 증명자는 스스로 생성한 일부의 증거와 검증자의 도전적 문제에 답하는 증거를 합쳐 최종 증거를 검증자에게 제출한다. 대화형 영지식 증명의 가장 쉬운 예는 알리바바 동굴 (The Ali Baba cave) 문제[^1]이며, 더욱 실용적인 예는 Schnorr 인증 (identification) 프로토콜이다. 알리바바 동굴에 관한 해설은 <https://blockgeeks.com/guides/what-is-zksnarks/> 에 친절하게 설명되어 있다. 그리고 이러한 대화형 영지식 증명 프로토콜의 한 일반화로써 시그마($$\sum $$) 프로토콜이 있다[^2].

비대화형(Non-interactive) 영지식 증명(NIZK, non-interactive zero knowledge)은 이름 그대로 증거 생성과정에서 검증자와 증명자의 대화가 필요 없는 증명을 의미한다. 즉, 증명자 혼자서 증거를 생성하는 것이다. 앞서 다룬 대화형 영지식 증명에서는 증거 생성과정에서 증명자가 검증자의 도전적 문제 (challenge)를 받고 이에 응해야 하기때문에 둘 간의 "대화 (conversation)"가 필요했다. 이 대화 과정을 생략하면서 그 역할을 유지하기 위해 제안된 방법 중 하나가 Fiat-Shamir heuristic[^3]이다. Fiat-Shamir heuristic은 "random oracle" 이라는 이상적인 랜덤 프로그램의 존재를 가정하여 검증자의 도전적 문제를 대체한다. 다시 말해, 증명자 스스로 도전적 문제를 랜덤하게 생성 한 후 이를 공개해 누구나 납득할 수 있게 하는것이다.

NIZK의 이점은 증명 과정에서 시간적 제약이 사라지는 것이며, 따라서 증명 프로토콜이 블록체인에 적용되기 적합하다. 대화형 영지식 증명의 경우 증명자와 검증자간의 대화가 필요했기 때문에, 증명을 완료하기 위해서는 두 사람이 같은 시간대에 서로 약속된 온라인 공간에 함께 접속해야하는 시공간적 제약이 있었다. NIZK는 더 이상 대화과정이 필요 없기 때문에, 증명자가 원하는 시간과 장소에 증거 파일을 업로드 해 놓으면, 검증자가 언제든 파일을 다운받아 검증을 수행 할 수 있다. No-interaction 특성은 특히 영지식 증명이 블록체인에 적용될 때 반드시 필요하다. 블록체인은 그 정의에 의해, 사용자가 데이터 업로드를 요청하면, 채굴자 혹은 관리자가 요청된 데이터를 모은 후 어느정도의 시간이 지난 후에 검증 및 업로드를 완료한다. 즉, 사용자가 증명자라면, 증거를 업로드 한 후 언제가 될 지 모를 시간 후에 검증자가 검증을 수행한다는 의미이다. 증명자가 NIZK 프로토콜을 따른다면 그 시간을 온라인상에서 기다리고 있을 필요가 없다.

[^1]: Quisquater, Jean-Jacques; Guillou, Louis C.; Berson, Thomas A, "How to Explain Zero-Knowledge Protocols to Your Children," 1990
[^2]: Ivan Damg˚ard, "On On Σ-protocols," *CPT*, 2010, online (<https://www.cs.au.dk/~ivan/Sigma.pdf>).
[^3]: https://en.wikipedia.org/wiki/Fiat%E2%80%93Shamir_heuristic
