---
title: multi-thread 구현하기 in c
tags: pthread miniRT
---

- CPU를 사용한 랜더링에 대해 시간을 단축시키기 위한 목적으로, multithreaded rendering을 구현한다
- 사용 환경
  - mac OS Big sur 11.2.3
  - Apple M1 (8core)
- 멀티쓰레딩이라는 것을 간단하게 구현해봄

  - 구체적으로 쓰레드가 무엇인지 이런 것 부터 설명해야겠지만,
  - 보다 고급적인 것은 `식사하는 철학자 문제`에서
    - 여기서는 단순히 합문제를 처리함
    - `Mutex`나 `세마포어`같은 개념은 여기서 다루지 않음
  - 쓰레드로 나눠서 `하나의 CPU`만 순차적으로 연산하는게 아니라,
  - `여러개의 CPU`가 하나의 일을 분담해서 작업하게 해줄 것이다.
- 병렬 프로그래밍은 어려움

  - 아래와 같은 계산이 필요하다고 해보자.
  - `r1 = r2 x r3`
  - `r4 = r1 + 5`
  - `r6 = r2 + 1`
  - `2번`식은 `1번`식에 의존하지만, `3번`식은 의존성을 가지지 않음.
  - `의존성`을 따져, 순차적이 아닌 병렬로 계산할 수 있음.
  - 합문제가 가장 간단히 병렬로 구성할 수 있음
  - 이 글에서도 합문제를 예제로 구현해볼 생각
  - 하지만 같은 문제를 1억번씩 더하는 식보다는 실용적인게 좋을 것 같아서 찾아봄

- 여기서는 `π = 3.14159265359` 를 구해볼 것이다.

  - 그레고리-라이프니츠 급수

  - $$
    \frac{\pi}{4} = 1 - \frac{1}{3} + \frac{1}{5} - \frac{1}{7} + \frac{1}{9} + \cdots
    $$

  - 17세기에 발견된 가장 아름다운 급수라고 한다(?)

  - 이 급수는 수렴속도가 매우 느린데, 

  - 5천번째 항까지 더해도 소수점 아래 2자리 까지 밖에 못구한다.

  - 1천억째 항까지 더하니 소수점 아래 10자리까지 얻을 수 있었다.

    - `real = 3.1415926535897`
    - `appr = 3.1415926535958`

- 본인도 멀티쓰레드를 구현하는 것은 처음이라

  - 일단 구현부터하고, 설명은 뒤로



# 급수만을 구현한 Ver

```C
#include <stdio.h>

long	ft_atoi(const char *str)
{
	unsigned long	i;
	int				sign;
	long			num;

	i = 0;
	sign = 1;
	num = 0;
	while ((str[i] >= 9 && str[i] <= 13) || str[i] == ' ')
		i++;
	if (str[i] == '+' || str[i] == '-')
	{
		if (str[i] == '-')
			sign = -1;
		i++;
	}
	while (str[i] >= '0' && str[i] <= '9')
	{
		if (num < 0 && sign < 0)
			return (0);
		if (num < 0 && sign > 0)
			return (-1);
		num = num * 10 + (str[i] - '0');
		i++;
	}
	return (sign * num);
}

int		ft_find_pi(long count)
{
	long 	i;
	double	pi;

	i = 1;
	pi = 0;
	while (i <= count)
	{
		if (i & 1)
			pi += (double)1 / (2 * i - 1);
		else
			pi -= (double)1 / (2 * i - 1);
		i++;
	}
	printf("%15ldth pi: %.30f\n", i - 1, 4 * pi);
	return (0);
}

int		main(int argc, char *argv[])
{
	long	count;
	long	max;

	count = 1;
	max = ft_atoi(argv[1]);
	while (count <= max)
	{
		ft_find_pi(count);
		count *= 2;
	}
	return (0);
}
```

![](https://images.velog.io/images/papawolf/post/a1b42d46-ce7d-4fda-83df-bc98bf3e52c1/Screen%20Shot%202021-04-05%20at%2010.11.32%20PM.png)

- 다음 처럼 값이 π에 수렴함을 볼 수 있다.
- 5천만번째까지 수초안에 계산하는 모습을 볼 수 있다.



# 시간측정한 Ver

```C
#include <stdio.h>
#include <time.h>

long	ft_atoi(const char *str)
{
	unsigned long	i;
	int				sign;
	long			num;

	i = 0;
	sign = 1;
	num = 0;
	while ((str[i] >= 9 && str[i] <= 13) || str[i] == ' ')
		i++;
	if (str[i] == '+' || str[i] == '-')
	{
		if (str[i] == '-')
			sign = -1;
		i++;
	}
	while (str[i] >= '0' && str[i] <= '9')
	{
		if (num < 0 && sign < 0)
			return (0);
		if (num < 0 && sign > 0)
			return (-1);
		num = num * 10 + (str[i] - '0');
		i++;
	}
	return (sign * num);
}

int		ft_find_pi(long count)
{
	long 	i;
	double	pi;

	i = 1;
	pi = 0;
	while (i < count)
	{
		if (i & 1)
			pi += (double)1 / (2 * i - 1);
		else
			pi -= (double)1 / (2 * i - 1);
		i++;
	}
	printf("%15ldth pi: %.30f ", i - 1, 4 * pi);
	return (0);
}

int		main(int argc, char *argv[])
{
	long	count;
	long	max;
	float	start, end;

	count = 1;
	max = ft_atoi(argv[1]);
	while (count <= max)
	{
		start = clock();
		ft_find_pi(count);
		end = clock();
		printf("time : %.3fs\n", (end - start)/1000000);
		count *= 2;
	}
	return (0);
}
```

![](https://images.velog.io/images/papawolf/post/ea4c93c9-e2fa-4c0d-a324-7f29404a0a53/Screen%20Shot%202021-04-05%20at%2010.24.15%20PM.png)

- `times.h`로 시간을 측정함
- 1000조를 인자로 전달했는데, 5497억번째 인자를 계산하는데 40분(2357s) 정도 걸렸다



# 멀티쓰레드 Ver

```C
#include <stdio.h>
#include <time.h>
#include <pthread.h>

#define THREAD_NUM 8

typedef struct	s_sum
{
	double		sum;
	long		start;
	long		end;
}				t_sum;

long	ft_atoi(const char *str)
{
	unsigned long	i;
	int				sign;
	long			num;

	i = 0;
	sign = 1;
	num = 0;
	while ((str[i] >= 9 && str[i] <= 13) || str[i] == ' ')
		i++;
	if (str[i] == '+' || str[i] == '-')
	{
		if (str[i] == '-')
			sign = -1;
		i++;
	}
	while (str[i] >= '0' && str[i] <= '9')
	{
		if (num < 0 && sign < 0)
			return (0);
		if (num < 0 && sign > 0)
			return (-1);
		num = num * 10 + (str[i] - '0');
		i++;
	}
	return (sign * num);
}

void	*ft_find_pi(void *sum)
{
	long 	i;
	long	end;
	double	pi;
	t_sum	*arg;

	arg = (t_sum *)sum;
	i = (long)arg->start;
	end = (long)arg->end;
	printf("start : %ld\n", i);
	printf("end : %ld\n", i);
	while (i < end)
	{
		if (i & 1)
			arg->sum += (double)1 / (2 * i - 1);
		else
			arg->sum -= (double)1 / (2 * i - 1);
		i++;
	}
	return (NULL);
}

void	ft_init_arg(int i, t_sum *sum, long count)
{
	sum->sum = 0;
	sum->start = i * (count / THREAD_NUM) + 1;
	sum->end = (i + 1) * (count / THREAD_NUM) + 1;
}

void	ft_multi_thread(long count)
{
	pthread_t	threads[THREAD_NUM];
	t_sum		sum[THREAD_NUM];
	long		i;
	double		pi;

	i = 0;
	while (i < THREAD_NUM)
	{
		ft_init_arg(i, &sum[i], count);
		i++;
	}
	i = 0;
	while (i < THREAD_NUM)
	{
		pthread_create(&threads[i], NULL, ft_find_pi, &sum[i]);
		i++;
	}
	i = 0;
	while (i < THREAD_NUM)
	{
		pthread_join(threads[i], NULL);
		i++;
	}
	i = 0;
	pi = 0;
	while (i < THREAD_NUM)
	{
		pi += sum[i].sum;
		i++;
	}
	printf("%15ldth pi: %.30f ", count, pi * 4);
}

int		main(int argc, char *argv[])
{
	long	count;
	long	max;
	float	start, end;

	count = 1;
	max = ft_atoi(argv[1]);
	while (count <= max)
	{
		start = clock();
		ft_multi_thread(count);
		end = clock();
		printf("time : %.3fs\n", (end - start)/1000000/THREAD_NUM);
		count *= 2;
	}
	return (0);
}

```

![](https://images.velog.io/images/papawolf/post/cfb75f0c-8ce1-4382-b693-2f1119bd317e/Screen%20Shot%202021-04-05%20at%2011.06.20%20PM.png)



# 비교

| 단일 쓰레드                                                  | 8개 쓰레드                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://images.velog.io/images/papawolf/post/ea4c93c9-e2fa-4c0d-a324-7f29404a0a53/Screen%20Shot%202021-04-05%20at%2010.24.15%20PM.png) | ![](https://images.velog.io/images/papawolf/post/cfb75f0c-8ce1-4382-b693-2f1119bd317e/Screen%20Shot%202021-04-05%20at%2011.06.20%20PM.png) |


- m1 맥을 사용하고 있고, 8개의 코어를 사용하기 때문에
  - 8개의 쓰레드를 잡았다.
- 약 2.7~3.2배 정도 빨라짐을 알 수 있다. 
- 값이 약간 다른데, 이는 부동소수점의 정확도가 15자리 정도인데
  - 연산과정이 조금 다른 부분에서 기인하는 것 같음.




# 주석

```C
#include <stdio.h>
#include <time.h>
#include <pthread.h>

#define THREAD_NUM 8

typedef struct	s_sum
{
	double		sum;
	long		start;
	long		end;
}				t_sum;

long	ft_atoi(const char *str);
{
// 생략
}

// pi를 찾는 함수
void	*ft_find_pi(void *sum)
{
	long 	i;
	long	end;
	double	pi;
	t_sum	*arg;

	arg = (t_sum *)sum;
	i = (long)arg->start;
	end = (long)arg->end;
	printf("start : %ld\n", i);
	printf("end : %ld\n", i);
	while (i < end)
	{
		if (i & 1)
			arg->sum += (double)1 / (2 * i - 1);
		else
			arg->sum -= (double)1 / (2 * i - 1);
		i++;
	}
	return (NULL);
}

// arg를 초기화해주는 함수, 누적값을 0
// 그리고 각 쓰레드가 연산해야할 구간을 나눠준다.
void	ft_init_arg(int i, t_sum *sum, long count)
{
	sum->sum = 0;
	sum->start = i * (count / THREAD_NUM) + 1;
	sum->end = (i + 1) * (count / THREAD_NUM) + 1;
}

// 멀티쓰레딩을 처리하는 부분
// 4개의 반복문을 살펴보자.
void	ft_multi_thread(long count)
{
	pthread_t	threads[THREAD_NUM];
	t_sum		sum[THREAD_NUM];
	long		i;
	double		pi;

	i = 0;
	while (i < THREAD_NUM)//인자로 넘겨줄 구조체를 초기화해줌
	{
		ft_init_arg(i, &sum[i], count);
		i++;
	}
	i = 0;
	while (i < THREAD_NUM)// 쓰레드를 만들고, 함수포인터로 함수를 넘겨줌, 인자인 구조체도 함께 넘겨줌.
	{
		pthread_create(&threads[i], NULL, ft_find_pi, &sum[i]);
		i++;
	}
	i = 0;
	while (i < THREAD_NUM)// 쓰레드가 종료되길 기다림. 8개를 위에서 만들어주고, 여기서 8개를 wait함
	{
		pthread_join(threads[i], NULL);
		i++;
	}
	i = 0;
	pi = 0;
	while (i < THREAD_NUM)// 여기는 그간 구한 pi를 더하고 출력해주는 곳
	{
		pi += sum[i].sum;
		i++;
	}
	printf("%15ldth pi: %.30f ", count, pi * 4);
}

int		main(int argc, char *argv[])
{
	long	count;
	long	max;
	float	start, end;

	count = 1;
	max = ft_atoi(argv[1]);
	while (count <= max)
	{
		start = clock();
		ft_multi_thread(count);
		end = clock();
		printf("time : %.3fs\n", (end - start)/1000000/THREAD_NUM);
		count *= 2;
	}
	return (0);
}
```



- `pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine)(void *), void *arg);`
  - `thread`: 사용할 쓰레드를 넣어준다.
  - `attr`: 쓰레드의 특성을 지정하는데, 기본 특성을 사용했기에 `NULL`을 넣어주었다.
  - `(*start_routine)`: 분기시켜 실행할 쓰레드 함수
  - `arg`: start_routine 함수에 매개변수로 넘겨줌



# 참고한 사이트

라이프니츠 급수 https://jjycjnmath.tistory.com/390


