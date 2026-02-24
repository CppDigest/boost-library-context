# #153 Fix deprecated copy warnings [Open]

> Username: apolukhin  
> Created at: 2022-01-17 13:54:32 UTC  
> Updated at: 2024-02-15 03:55:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/153  

All the implicit special member functions made explicit in this PR.  
It fixes the compilation and test runs with `./b2 libs/numeric/ublas/test/ cxxstd=17 "cxxflags=-Werror=deprecated-copy" toolset=clang-13 -j4`

---

## Comment 1

> Username: amitsingh19975  
> Created_at: 2022-02-07 09:14:50 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1031236015  

If I'm correct, it won't be a problem if we follow the rule of six. This warning arises when you give the copy assignment constructor without providing the default copy constructor. I assume the ublas code base follows the rule of six. If it does not, we have to fix that.  
  
[Godbolt](https://godbolt.org/z/T858Yc3Tz)

---

## Comment 2

> Username: apolukhin  
> Created_at: 2022-02-07 09:55:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1031274510  

Yes, you are right. This PR adds the missing functions for C++11

---

## Comment 3

> Username: amitsingh19975  
> Created_at: 2022-02-07 10:00:12 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1031278967  

> Yes, you are right. This PR adds the missing functions for C++11  
  
Ok.

---

## Review 4 [Approved]

> Username: amitsingh19975  
> Created_at: 2022-02-07 10:01:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/153#pullrequestreview-874436774  

It looks good, and thanks for contributing.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2022-05-19 09:22:36 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1131455958  

Gentle reminder

---

## Comment 6

> Username: apolukhin  
> Created_at: 2022-06-05 11:51:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1146790523  

@bassoy gentle reminder

---

## Comment 7

> Username: apolukhin  
> Created_at: 2022-09-02 16:12:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1235680793  

@amitsingh19975 could you please somehow speedup the PR review process?

---

## Comment 8

> Username: amitsingh19975  
> Created_at: 2022-09-02 17:17:28 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1235738752  

It's up to others. I cannot do anything. I would recommend contacting @bassoy via email or someone who has much more power than me.

---

## Comment 9

> Username: apolukhin  
> Created_at: 2024-02-14 10:49:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/153#issuecomment-1943515255  

@bassoy Please merge

---
