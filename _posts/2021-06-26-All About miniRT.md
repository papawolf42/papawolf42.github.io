---
title: All About miniRT(1)
tags: [All About, 42Seoul, miniRT, ray tracing]
---

개인적으로는 42서울 본과정에서 `miniRT` 이전과 이후로 나뉜다고 말할 수 있을 정도로, 저를 성장시켜준 과제입니다.

miniRT는 광선 추적(Ray trace)을 사용하여, 3d object를 그리는 프로젝트입니다.

subject에서는 이 과제 이후 2가지가 바뀔 것이라고 언급되어있습니다.

1. 예쁜 바탕화면을 가질 수 있음.
2. 수학 공식들을 코드로 바꾸는데 두려움이 사라질 것.

![](https://lh3.googleusercontent.com/pw/AM-JKLWrdoqXRjVog-ZfhG9uewVUhCJgOOqiUTrQvT2i5OdEVINWQekTGU3Iz7V3rwD95KmpgyDpo0B_RHpYxou55M0IppwDDGQoC7kC6EoQDbBnFG1dzRfcGcOfXn2wAMu7EfAvkf8zVP57qZv9njGOBn8k=w3008-h3062-no?authuser=3)

suhshin님이 디자인하신 장면입니다.



miniRT를 했던 경험을 조금 나누려고 합니다. 

1. subject 둘러보기
2. 선형대수학 공부는 어떤식으로?
3. 참고한 사이트 추천(ray tracing in one weekend, scratch a pixel, 다양한 git 등등)
4. 오른손 좌표계와 왼손 좌표계
5. 오브젝트를 그리는 법
   1. 원을 그리는 법
   2. 광원을 적용하는 법
      1. 퐁 모델
      2. 거리에 반비례한 광원의 감쇠
   3. 평면을 그리는 법
   4. 사각형을 그리는 법
   5. 삼각형을 그리는 법
   6. 원통을 그리는 법
6. 안티 엘리어싱(계단 현상 줄이기)
7. 회전(rotation)과 이동(transformation)

 이 외에도 parsing 구현, 키보드 입력의 처리, bmp format 출력 등은 RT와 관련이 적기도하고, 난이도가 어려운 개념이 아니라 다루지 않으려고 한다.

 All About 시리즈로 본격적으로 적으려고 생각하기에 천천히 완성해나갈 생각이다.