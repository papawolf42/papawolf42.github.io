---
title: git submodule 적용하기
tags: "git submodule"
---

# git submodule 만들기

![image-20210602222714224](/Users/gunkim/Library/Application Support/typora-user-images/image-20210602222714224.png)

![Screen Shot 2021-06-20 at 11.40.48 PM](/Users/gunkim/Pictures/capture/Screen Shot 2021-06-20 at 11.40.48 PM.png)

위의 화면을 아래와 같이 바꾸는 것을 해볼 것이다.

위 상황을 설명하자면, 42서울의 과제를 하나씩 해결하다보니 한가지 딜레마를 만나게 되었다. 과제 그 자체가 라이브러리였고, 이 라이브러리를 다른 과제에서 중복되게 사용하는 것이다.

00_Libft는 나만의 라이브러리로, 42서울 본과정에서의 첫번째 과제이자, 첫 라이브러리였다. 다른 과제를 할때, 이 libft라는 라이브러리를 사용하는 경우가 많다. 마찬가지 get_next_line 역시 다른 과제에서 필요한 경우가 많았다.

그래서 아래와 같은 문제가 발생한다.

```c++
.
├── 00_Libft // 한번
├── 01_ft_printf
│   └── libft // 두번
├── 01_get_next_line
├── 02_ft_server
├── 02_miniRT
│   └── lib
│       ├── get_next_line
│       └── libft // 세번
└── 02_push_swap
    └── lib
        └── libft // 네번
```


만들어둔 libft는 다른 프로젝트들에서 subdirectory로 두고 사용하고 있다.
ft_printf와 miniRT에서 사용했는데, 앞으로 진행해야할 과제 push_swap에서도 써야할 판국이다.
그렇게 되면 이 저장소에만 같은 크기의 공간을 차지하는 libft가 4개나 중복되어 존재하는 것이다.
이것 말고도 다른 문제점들이 존재한다.

1. 수정된 내용이 반영되지 못한다.
   root에 존재하는 00_Libft를 수정하게 되더라도, 서브프로젝트의 libft들에게 반영되지 못한다.
2. 같은 내용이 중복되므로, 저장소의 용량을 낭비하게 된다.
   워낙 작은 라이브러리라 낭비랄 것도 없지만, 이게 큰 프로젝트라면 용량을 생각하는게 맞을 것이다.

이를 처리해주는 git의 기능이 있는데, 바로 submodule이다.

프로젝트에 외부에서 개발한 라이브러리라던가, 내부 여러 프로젝트에 공통으로 사용하는 라이브러리가 있는 경우, 이 프로젝트들을 독립된 저장소로 관리할 수 있게 해준다. 그리고 부모 저장소에서 자식 저장소를 포함할 수 있다.

submodule 외에 subtree라는 기능도 존재한다. 이 둘은 비슷한 개념이면서, 살짝 다른데, 시간이 되면 따로 포스팅하고 싶다. 간단하게는 

- submodule은 CBD(Component-Based Development)이며, 링크이다.
- subtree는 SBD(System-Based Development)이며, 복사이다.
- [출처](https://flowerinmyheart.tistory.com/entry/Subtree-Submodule-1)
- submodule을 사용하기로 한다. 최상위 프로젝트인 42Cursus는 System이라기보다는 Component에 가깝다. ft_server 과제가 miniRT에 안쓰이며, miniRT가 push_swap에 필요하지 않기에
- 이정도로만 정리한다.



## push_swap은 submodule로 빼야하는가?

- push_swap은 다른 곳에서 참조하지 않음. 즉 하나의 프로젝트

### 장점

- git history를 보기 편함.

### 단점

- repository가 복잡해져보이는 효과를 가짐.



## 이미 존재하는 subdirectory인 00_Libft를 submodule로 만들기

- reference - https://stackoverflow.com/questions/17413493/create-a-submodule-repository-from-a-folder-and-keep-its-git-commit-history

- ```shell
  .
  ├── 00_Libft
  ├── 01_ft_printf
  ├── 01_get_next_line
  ├── 02_ft_server
  ├── 02_miniRT
  ├── 42_logo_black.svg
  └── README.md
  ```

  위와 같은 구조인데 여기서 00_Libft를 독립된 Repo로 만들고, submodule로 추가할 것이다.

- git filter-branch는 너무나 강력한 명령어

- 성공한 뒤의 모습
  ![image-20210603120300123](/Users/gunkim/Library/Application Support/typora-user-images/image-20210603120300123.png)

- 이제 마저 남은 01_get_next_line도 submodule로 만들자.

### 기존의 디렉토리를 새로운 git repo 만들기




