---
layout: post
title: Time data structure note
description: "一些关于时间的数据结构的笔记"
modified: 2014-11-14
category: articles
tags: [C]
comments: true
share: true
---

time_t

typesize.h>

~~~
#define __TIME_T_TYPE           __SLONGWORD_TYPE
types.h>

#define __SLONGWORD_TYPE        long int
__STD_TYPE __TIME_T_TYPE __time_t;      /* Seconds since the Epoch.  */
~~~

timeval

time.h>

~~~
/* A time value that is accurate to the nearest
   microsecond but also has a range of years.  */
struct timeval
  {
    __time_t tv_sec;            /* Seconds.  */
    __suseconds_t tv_usec;      /* Microseconds.  */
  };
~~~

timespec

~~~
/* POSIX.1b structure for a time value.  This is like a `struct timeval' but
   has nanoseconds instead of microseconds.  */
struct timespec
  {
    __time_t tv_sec;            /* Seconds.  */
    long int tv_nsec;           /* Nanoseconds.  */
  };
~~~

struct tm

~~~
/* Used by other time functions.  */
struct tm
{
  int tm_sec;                   /* Seconds.     [0-60] (1 leap second) */
  int tm_min;                   /* Minutes.     [0-59] */
  int tm_hour;                  /* Hours.       [0-23] */
  int tm_mday;                  /* Day.         [1-31] */
  int tm_mon;                   /* Month.       [0-11] */
  int tm_year;                  /* Year - 1900.  */
  int tm_wday;                  /* Day of week. [0-6] */
  int tm_yday;                  /* Days in year.[0-365] */
  int tm_isdst;                 /* DST.         [-1/0/1]*/

#ifdef  __USE_BSD
  long int tm_gmtoff;           /* Seconds east of UTC.  */
  __const char *tm_zone;        /* Timezone abbreviation.  */
#else
  long int __tm_gmtoff;         /* Seconds east of UTC.  */
  __const char *__tm_zone;      /* Timezone abbreviation.  */
#endif
};
~~~
