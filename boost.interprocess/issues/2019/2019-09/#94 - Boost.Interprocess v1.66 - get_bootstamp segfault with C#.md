# #94 - Boost.Interprocess v1.66 - get_bootstamp segfault with C# [Open]

> Username: koudis  
> Created at: 2019-09-24 10:59:03 UTC  
> Updated at: 2021-04-29 20:26:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/94  

Hello,  
  
I have problem with Boost Interprocess. I create C DLL Library which I Marshall  from C to C#.  
  
I described problem in [StackOwerflow](https://stackoverflow.com/questions/49383042/boost-interprocess-v1-66-get-bootstamp-segfault-with-c-sharp)  
  
Is this behaviour bug or my fault? What i'm doing wrong?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-09-26 21:34:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/94#issuecomment-535694868  

It's very hard difficult to deduce the source of the error from your report. Have you been able to get some kind of minimal test example that reproduces the problem?

---

## Comment 2

> Username: jaredhagel  
> Created at: 2021-04-29 20:17:41 UTC  
> Updated at: 2021-04-29 20:26:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/94#issuecomment-829567249  

I'm not sure the source of the error either, but I made the same change as described in the stack overflow post and this addressed my issue. I couldn't get around this error otherwise - even rebooting my Windows system didn't help.  
I should mention that our project is written entirely in C++ (ie no C# is involved).
