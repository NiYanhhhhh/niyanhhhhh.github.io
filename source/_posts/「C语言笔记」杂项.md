---
title: 「C语言笔记」杂项
tags:
  - C语言
  - Clearning
date: 2023-04-11 02:39:26
---

<a href="/clearning/" title="目录">目录</a>

创建日期：2023-04-11 02:39:26

> *这里记录着一些C语言写程序过程中容易忘记的东西。*

持续更新中...

### 关于时间

#### 使用<sys/time.h>和gettimeofday(struct timeval\*, void \*)

返回自1970年1月1日UTC时间00:00:00起经过的时间，第一个变量为传入时间的指针，结构为
```c
struct timeval
{
#ifdef __USE_TIME_BITS64
  __time64_t tv_sec;		/* Seconds.  */
  __suseconds64_t tv_usec;	/* Microseconds.  */
#else
  __time_t tv_sec;		/* Seconds.  */
  __suseconds_t tv_usec;	/* Microseconds.  */
#endif
};
#endif
```

#### 使用<time.h>和clock(void)

返回自程序开始执行以来的时钟滴答数。如果将其除以常数CLOCKS_PER_SEC，将获得程序在CPU中已运行多长时间（以秒为单位）。

#### 代码示例

以1到1000,000,000求和为例

{% codeblock lang:c tictoc.h %}
#include <time.h>
#include <sys/time.h>

#ifndef HEADER_FILE_TICTOC
#define HEADER_FILE_TICTOC

clock_t __clock_begin;
struct timeval __time_begin, __time_end;

#define tic __clock_begin=clock()
#define toc if (__clock_begin == -1) printf("toc error!\n"); \
                               else printf("clock time used: %.3fms\n", (float) (clock() - __clock_begin)/CLOCKS_PER_SEC*1000); __clock_begin=-1

#define timetic gettimeofday(&__time_begin, 0);
#define timetoc gettimeofday(&__time_end, 0); printf("real time used: %.3fms\n", 1000*((__time_end.tv_sec - __time_begin.tv_sec) + (__time_end.tv_usec - __time_begin.tv_usec)*1e-6))

#endif
{% endcodeblock %}

{% codeblock lang:c gettime.c %}
#include <stdio.h>
#include "tictoc.h"

int main () {
    // Start measuring time
    tic;
    timetic;
    
    long int n = 1000*1000*1000;
    long long int sum = 0;
    for (int i=0; i<n; sum += ++i);
    
    printf("Result: %lld\n", sum);

    // Stop measuring time and calculate the elapsed time
    toc;
    timetoc;
    
    return 0;
}
{% endcodeblock %}
