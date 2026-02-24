# #282 ofz#4303 avoid signed integer overflow [Closed]

> Username: dtardon  
> Created at: 2017-11-18 09:16:21 UTC  
> Updated at: 2019-05-14 23:38:51 UTC  
> Closed at: 2019-05-14 23:38:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/282  

/usr/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:110:26: runtime error: signed integer overflow: -2147483647 - 19 cannot be represented in type 'int'

---

## Comment 1

> Username: dtardon  
> Created_at: 2017-11-18 09:16:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-345429655  

This has been found by oss-fuzz. Standalone reproducer: https://paste.fedoraproject.org/paste/Ab2L-UxCcjsJ1UDUOQ8jdw

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-11-18 11:56:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-345437501  

It looks like `x3` has the same problem or comment should be added https://github.com/boostorg/spirit/blob/1c8d9b6e25da80d414eb59709f41acb7d2c67e55/include/boost/spirit/home/x3/support/numeric_utils/extract_real.hpp#L62-L67  
  
@djowel is the a reason why `qi` and `x3` does not share numeric parsing code? I find it ~80% identical.

---

## Comment 3

> Username: kcc  
> Created_at: 2017-11-18 18:34:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-345461638  

FYI: we are already fuzzing parts of boost on oss-fuzz: https://github.com/google/oss-fuzz/tree/master/projects/boost  
Other parts, such as spirit, are welcome too.

---

## Comment 4

> Username: djowel  
> Created_at: 2017-11-18 23:45:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-345480025  

@Kojoley, the plan was to cleanup "support" and merge them. X3 "support" is cleaner and was meant to be the replacement for Qi support, somehow. I wanted to keep X3 clean.

---

## Comment 5

> Username: djowel  
> Created_at: 2017-11-18 23:48:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-345480181  

Oh, and before I forget, we need to add test cases for issues like these to make sure we won't do anything to break it in the future, esp. if we are going to merge X3 with Qi support. PRs such as these should be backed up by a test case that exhibits the behavior (of mis-behavior :)).

---

## Comment 6

> Username: djowel  
> Created_at: 2019-05-05 14:25:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-489431289  

> Oh, and before I forget, we need to add test cases for issues like these to make sure we won't do anything to break it in the future, esp. if we are going to merge X3 with Qi support. PRs such as these should be backed up by a test case that exhibits the behavior (of mis-behavior :)).  
  
@dtardon can you do that ^ ? We need a test case that exhibits the problem this issue is fixing.

---

## Comment 7

> Username: djowel  
> Created_at: 2019-05-14 23:38:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/282#issuecomment-492449458  

I'm closing this one. A better way to address this is to not allow such big digits in the default real number policies for primitive floating point numbers. The current code (real policies) limits the number of digits to traits::max_digits10<T>::value. The new ignore_excess_digits policy should also have a limit as well.  
  
See https://github.com/boostorg/spirit/issues/508

---
