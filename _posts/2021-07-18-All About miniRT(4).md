---
title: All About miniRT(3) - subject 둘러보기
tags: [All About, 42Seoul, miniRT, ray tracing]
---



- 광선 추적 자체가 3차원 공간을 생각해야하기에 vector라는 수학의 도구를 사용한다.

- 이 벡터의 도입하고 이해하기 위해서는 `"선형대수학"` 을 정복하고 지나가야한다.

- 이 유튜브 영상을 추천한다. 한국어 자막도 잘 구성되어 있고, 선형대수학의 기본과 심화를 모두 담으며, 신선한 통찰까지 준다.
  [3Blue1Brown - Essence of linear algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
  ![](https://i.imgur.com/UprkDt8.png)

- 말은 정복이지만, 우리가 사용할 것들에 대해서만 이해를 하면 된다.

- object의 `위치(position)`과 `방향(direction)`자체를 `벡터(vector)`로 나타낸다.

- 벡터나 좌표계의 `이동(transition)`과 `회전(rotation)`은 `선형변환(linear transformation)`으로 나타낸다.
  선형변환은 위 2가지(이동, 회전)외에도 축소, 확대를 할 수 있지만, miniRT 과제 자체에서는 object를 딱히 필요하지 않은 기능이다.

  