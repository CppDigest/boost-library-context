# #15 Variadic Templates based phoenix/core [Closed]

> Username: Flast  
> Created at: 2015-04-17 10:42:13 UTC  
> Updated at: 2016-02-21 14:07:58 UTC  
> Closed at: 2016-02-20 06:58:31 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15  

Hi Joel and John!  
  
I implement C++11 variadic templates based phoenix/core, please review if you are interested in.  
- NOTE:  
  1. Preprocessed files should be regenerated; I will push one more commit(4823cf9df210db95459bf8eab904e25746f8ddef) if PR is good for merging.  
  2. phoenix::expr_ext is not changed within this PR due to non-variadic Proto expr.  
- Tested  
  - GCC 4.9.2 C++98 / C++11 / C++1y  
  - LLVM Clang 3.6.0 C++98 / C++11 / C++14

---

## Comment 1

> Username: djowel  
> Created_at: 2015-04-17 11:38:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-93969904  

This is amazing, as always, Kohei!

---

## Comment 2

> Username: djowel  
> Created_at: 2015-04-17 11:39:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-93969983  

I'll review the PR in the weekend.

---

## Comment 3

> Username: fletchjp  
> Created_at: 2015-04-17 16:33:15 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-94022413  

Kohei and Joel  
  
That looks very interesting.  I have looked at variadics for some other codes but not yet for Phoenix, so this is very welcome.  Is it the idea that the interface to the user remains the same?  Does it provide any extra facilities?  Are any new tests needed, or any modifications to existing tests?  
  
On a practical level, can I ask you (Joel) to deal with the pull request to develop if you think that is O.K.  As I think I told you before, I have never found out how to deal with pull requests, and I cannot find the information on line in Boost.  
  
Up until now there is nothing in Phoenix to my knowledge to take advantage of C++11 or C++14.  Obviously there are things which can be done (e.g. lambdas) which are related.  
  
I am not aware of a clear policy yet in Boost as a whole on how to exploit C++11.  There has been some suggestion of new versions of libraries which are no longer compatible with C++ before C++11.  
  
I would be interested to discuss this.  
  
Thanks and best wishes  
  
John  
  
---  
  
From: Joel de Guzman [notifications@github.com]  
Sent: 17 April 2015 12:39  
To: boostorg/phoenix  
Subject: Re: [phoenix] Variadic Templates based phoenix/core (#15)  
  
I'll review the PR in the weekend.  
  
�  
Reply to this email directly or view it on GitHubhttps://github.com/boostorg/phoenix/pull/15#issuecomment-93969983.

---

## Comment 4

> Username: Flast  
> Created_at: 2015-04-18 14:00:16 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-94166680  

Hi John,  
  
> Is it the idea that the interface to the user remains the same?  
  
Yes, no existing code changing is required.  
  
> Does it provide any extra facilities?  Are any new tests needed, or any modifications to existing tests?  
  
This PR should provide completely same semantics and should work with existing tests. In C++98 (i.e. no variadic templates enabled env.), the code fallback into traditional preprocessed headers.  
  
Thanks,  
  
Kohei

---

## Comment 5

> Username: Flast  
> Created_at: 2016-02-19 07:24:25 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-186102116  

@djowel @fletchjp any progress? it has been past about a year...

---

## Comment 6

> Username: djowel  
> Created_at: 2016-02-19 08:08:33 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-186111237  

Sorry about that. It's difficult to review this all from the github interface, alas. How about we do it in a branch so I can have more time to scrutinize the code?

---

## Comment 7

> Username: Flast  
> Created_at: 2016-02-19 08:18:36 UTC  
> Updated_at: 2016-02-19 08:23:33 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-186113915  

OK, creating a `variadics` branch is good to begin.

---

## Comment 8

> Username: Flast  
> Created_at: 2016-02-19 13:02:58 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-186205279  

@djowel so, could you create a new branch?

---

## Comment 9

> Username: djowel  
> Created_at: 2016-02-20 04:25:53 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-186503388  

Done: "variadics"

---

## Comment 10

> Username: Flast  
> Created_at: 2016-02-20 05:52:05 UTC  
> Url: https://github.com/boostorg/phoenix/pull/15#issuecomment-186517643  

Thanks!

---
