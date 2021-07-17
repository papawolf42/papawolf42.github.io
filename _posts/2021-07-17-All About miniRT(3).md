---
title: All About miniRT(3) - subject 둘러보기
tags: [All About, 42Seoul, miniRT, ray tracing]
---

- 2서클 그래픽 과제를 했다면 기본적인 사용법을 익힐 수 있을 것이라 생각됨
- mlx는 학생들을 위한 간단한 윈도우 인터페이스 라이브리러
- https://github.com/taelee42/mlx_example
  taelee님이 만드신 가이드 예제 01 ~ 06번 까지 따라 해보면 기본적인 사용법을 익힐 수 있음
  - 06번 예제는 cub3d 과제의 map을 시각화 하는데 도움이됨.
- intra의 동영상 강의의 내용도 taelee님의 예제에 존재.
- man으로 man page를 열어볼 수 있음. 이때, man command 뒤로 프로그램 이름이 아닌, 경로를 직접 지정해주어야함.
  ![](https://i.imgur.com/T4OBFGI.png)
- opengl 버전과 mms 버전는 차이가 몇가지 존재함
  1. 환경의 최대 해상도를 가져오는 함수가 존재
  2. data 배열에서 `(x * width + y)` 의 인덱스로 접근하는데, 이때 padding을 처리하는 방식이 다름.
  3. 2번의 이유로 bmp 포멧을 만들 때, mms 버전의 경우 padding을 고려해주는 작업이 별도로 필요.
- mlx 자체의 memory leak이 존재함.

