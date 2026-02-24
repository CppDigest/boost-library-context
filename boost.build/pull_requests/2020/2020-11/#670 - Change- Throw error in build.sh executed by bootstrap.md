# #670 Change: Throw error in build.sh executed by bootstrap. [Closed]

> Username: MaxSac  
> Created at: 2020-11-11 15:53:25 UTC  
> Updated at: 2021-10-02 20:59:50 UTC  
> Closed at: 2021-02-02 02:14:00 UTC  
> Url: https://github.com/boostorg/build/pull/670  

If an error occures due testing if the compiler is capable to build boost,  
it would be nice to locate the error.  
Otherwise it is more complicated than it should be to trace the error back.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-11-15 01:57:17 UTC  
> Url: https://github.com/boostorg/build/pull/670#issuecomment-727294430  

Although I see the advantage to showing the configure errors.. This solution to show them has some problems. When there are multiple compilers available and only one of them supports 11 you'll see bogus errors as each is tested. Also the error is going to appear a bit out of context, as it would be before the message saying 11 is not supported. Ideally we would output the error only when invoked on the final compiler check (https://github.com/boostorg/build/blob/develop/src/engine/build.sh#L203). Additionally it would be better if the error where printed at the tail of the 11 message as part of error_exit. Do you think you can do changes that achieve that?

---

## Comment 2

> Username: MaxSac  
> Created_at: 2020-11-16 09:11:34 UTC  
> Url: https://github.com/boostorg/build/pull/670#issuecomment-727844498  

These are all valid and good suggestions. I try to submit a first proposal until tomorrow evening.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2021-02-02 02:14:00 UTC  
> Url: https://github.com/boostorg/build/pull/670#issuecomment-771296742  

I resolved this a bit ago by rewriting the way build.sh works.

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:49 UTC  
> Url: https://github.com/boostorg/build/pull/670#issuecomment-932819701  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
