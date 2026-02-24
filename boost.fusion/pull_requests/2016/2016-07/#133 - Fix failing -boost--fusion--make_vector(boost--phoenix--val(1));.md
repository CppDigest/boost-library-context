# #133 Fix failing "boost::fusion::make_vector(boost::phoenix::val(1));" [Closed]

> Username: oxidase  
> Created at: 2016-07-21 12:56:26 UTC  
> Updated at: 2016-07-21 18:54:45 UTC  
> Closed at: 2016-07-21 18:54:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/133  

regression introduced in 79d8e9d11c07099bdf1db0724e8fb1ee92e98920  
  
/cc @vtnerd

---

## Comment 1

> Username: djowel  
> Created_at: 2016-07-21 15:50:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234296905  

What is the intent? Could you please provide a minimal test program?

---

## Comment 2

> Username: finjulhich  
> Created_at: 2016-07-21 15:56:38 UTC  
> Updated_at: 2016-07-21 15:57:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234298964  

```  
#include <iostream>  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/include/support_istream_iterator.hpp>  
#include <boost/phoenix.hpp>  
  
int main()  
{  
  std::string str;  
  auto iter = boost::phoenix::val(str.begin());  
  boost::fusion::make_vector(iter);  
  return 0;  
}  
  
```  
  
is failing on develop.  
  
I am about to try this PR now.

---

## Comment 3

> Username: djowel  
> Created_at: 2016-07-21 16:06:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234301846  

Was it not failing before? Which version of boost did this start to fail?

---

## Comment 4

> Username: finjulhich  
> Created_at: 2016-07-21 16:12:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234303684  

The above compiles after this PR.

---

## Comment 5

> Username: finjulhich  
> Created_at: 2016-07-21 16:20:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234306072  

boost 1.58 c=+11 g++5 it was working.  
boost 1.60 c++11 g++6.1 it failed.  
boost develop c++11 it failed  
boost develop+ this PR it works.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2016-07-21 17:15:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234321210  

This fix is insufficient for several reasons. The `is_convertible` call is using `Sequence` which has already been stripped of constness and references, so it always appears as an r-value to the trait. Also, nested sequences are still an issue for **two** reasons: (1) `is_convertible` always returns true for C++11 `fusion::vector`, and (2) this check with `vector<vector<int>> foo(list<vector<int>>())` reduces to `not_<not_<is_convertible<list<vector<int>>, int>>` which is also wrong. The latter issue has been a problem with a few containers, but has gone unnoticed (and therefore must be a rare case for users).  
  
I have a fix - the rough cut was posted on the mailing list - and I am slowly doing compile-time analysis, etc. before doing pull requests. I do not want to create _more_ problems like I did with my last patch. IMO, this patch should only be applied locally if it fixes issue in Boost 1.60 or Boost 1.61 that a particular user is having.  
  
If this request is merged, then tests demonstrating the prior problem and the correction should also be provided.

---

## Comment 7

> Username: oxidase  
> Created_at: 2016-07-21 18:54:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/133#issuecomment-234349276  

@finjulhich yes, that is exactly the issue  
  
@vtnerd the fix proposed by you worked well for me, but i did not now how to make it public, so i decided to create this PR.   
I will close the request for now, but keep the commit in my local branch  until  full fix will be committed.

---
