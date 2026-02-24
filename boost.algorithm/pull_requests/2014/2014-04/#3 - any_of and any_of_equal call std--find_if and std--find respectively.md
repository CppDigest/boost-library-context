# #3 any_of and any_of_equal call std::find_if and std::find respectively [Closed]

> Username: Kojirion  
> Created at: 2014-04-15 20:48:21 UTC  
> Updated at: 2022-08-15 03:24:56 UTC  
> Closed at: 2022-08-15 03:24:56 UTC  
> Url: https://github.com/boostorg/algorithm/pull/3  

any_of calls std::find and checks against range end. STL implementations should be trusted to perform this more efficiently than the hand-written search.  
  
All tests pass.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-04-15 20:58:37 UTC  
> Url: https://github.com/boostorg/algorithm/pull/3#issuecomment-40533505  

Except that they don't. Look at a standard library implementation of std::find (say, the one in libc++ at https://llvm.org/svn/llvm-project/libcxx/trunk/include/algorithm), and you'll see it's the exact same code.  
  
What you've added is the overhead of copying two iterators (and a predicate, in the second case), and an additional procedure call (which may be inlined by the compiler).  
  
I'm not strongly against this change, but I don't see the advantage of it, either.

---

## Comment 2

> Username: Kojirion  
> Created_at: 2014-04-15 21:24:47 UTC  
> Updated_at: 2014-04-15 21:25:28 UTC  
> Url: https://github.com/boostorg/algorithm/pull/3#issuecomment-40536440  

In this simple benchmark:  
  
https://gist.github.com/Kojirion/10776248  
  
the current implementation performs worse than the rest.  
The C++11 implementation of std::any_of my gcc (4.8) calls none_of which in turn does call find_if. These calls can be expected to get - and do get - inlined.

---

## Comment 3

> Username: Kojirion  
> Created_at: 2014-09-08 11:45:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/3#issuecomment-54806694  

Now that 1.56 is out, is there any chance this will be implemented?  
In this video (at 49:30):  
  
http://vimeo.com/97349218#at=2910  
  
Alexandrescu presents how to specialize find for random access iterators, claiming it is 3.8 times faster than a linear search.  
  
I can also conceive an STL implementation where find does not return early, so as not to leak information.  
And so on. Hand-writing another linear search disregards such specializations and choices.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-08-29 14:56:44 UTC  
> Url: https://github.com/boostorg/algorithm/pull/3#issuecomment-325690978  

I tried your benchmark, and I got different results - the current implementation was equal to the fastest.  
  
 0.000021s wall, 0.000000s user + 0.000000s system = 0.000000s CPU (n/a%)  
 0.000018s wall, 0.000000s user + 0.000000s system = 0.000000s CPU (n/a%)  
 0.000020s wall, 0.000000s user + 0.000000s system = 0.000000s CPU (n/a%)  
 0.000018s wall, 0.000000s user + 0.000000s system = 0.000000s CPU (n/a%)  
 0.000019s wall, 0.000000s user + 0.000000s system = 0.000000s CPU (n/a%)  
  
(clang trunk, Mac OS X, libc++, -O3)

---
