# #285 Added defect macro BOOST_NO_CXX11_HDR_EXCEPTION [Closed]

> Username: dmenendez-gruposantander  
> Created at: 2019-06-04 10:05:27 UTC  
> Updated at: 2019-08-23 11:46:49 UTC  
> Closed at: 2019-08-23 11:46:49 UTC  
> Url: https://github.com/boostorg/config/pull/285  

Attempt to solve issue #284

---

## Comment 1

> Username: dmenendez-gruposantander  
> Created_at: 2019-06-04 10:06:10 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-498608225  

Only tested locally on VS2015 and VS2017, not sure how I can test on other compilers that actually need the macro defined.  
Advice welcome.

---

## Comment 2

> Username: dmenendez-gruposantander  
> Created_at: 2019-06-04 10:38:12 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-498618175  

The travis CI is failing because I need to update the config for gcc in C++03 mode.  
I could give it a try but I'm not sure trial and error is the best way forward, specially without being able to test with gcc locally.

---

## Comment 3

> Username: dmenendez-gruposantander  
> Created_at: 2019-06-19 08:06:17 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-503454014  

OK, now I think all the tests should pass, but there seems to be something broken somewhere else in the CI tests.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-06-19 10:58:28 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-503512245  

>OK, now I think all the tests should pass, but there seems to be something broken somewhere else in the CI tests.  
  
There is, I'm working on it.

---

## Comment 5

> Username: dmenendez-gruposantander  
> Created_at: 2019-08-23 08:25:44 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-524223711  

> > OK, now I think all the tests should pass, but there seems to be something broken somewhere else in the CI tests.  
>   
> There is, I'm working on it.  
  
Now that the 1.71 release is out, how can I retry this PR?  
Should I rebase everything on the latest devel?  
Are the CI problems sorted out?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-08-23 10:12:11 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-524258767  

I'll sort that shortly, can I have your real name please?  The copyright on the test case should carry your name not Beman's.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-08-23 11:46:43 UTC  
> Url: https://github.com/boostorg/config/pull/285#issuecomment-524285347  

Replaced by https://github.com/boostorg/config/pull/293  
  
I still need a name though.

---
