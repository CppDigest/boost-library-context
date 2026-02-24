# #351 X3: Prevent parse_nan() from dereferencing out of range iterator [Merged]

> Username: oracleoftroy  
> Created at: 2018-01-04 23:33:39 UTC  
> Updated at: 2018-02-20 00:18:32 UTC  
> Merged at: 2018-01-05 23:20:55 UTC  
> Closed at: 2018-01-05 23:20:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/351  

Closes #350

---

## Comment 1

> Username: djowel  
> Created_at: 2018-01-05 22:09:46 UTC  
> Updated_at: 2018-01-05 22:09:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355678908  

@Kojoley, what do we do when "Some checks were not successful"? Surely this PR did not cause the failure, but what do I know. Hmmm...

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-01-05 22:16:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355680384  

I have restarted failed jobs, they were because of some network problems at travis end plus a warning from boost.tuple you had merged patch for.

---

## Comment 3

> Username: djowel  
> Created_at: 2018-01-05 22:22:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355681707  

> I have restarted failed jobs, they were because of some network problems at travis end plus a warning from boost.tuple you had merged patch for.  
  
Oh... are we set to fail on warnings?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-01-05 22:29:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355683009  

Yes, it is good thing to request patches that does not introduce new warnings. I wish other boost libraries do the same thing.

---

## Comment 5

> Username: djowel  
> Created_at: 2018-01-05 22:48:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355686565  

> Yes, it is good thing to request patches that does not introduce new warnings. I wish other boost libraries do the same thing.  
  
👍

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-01-05 23:22:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355692250  

Thanks @oracleoftroy!

---

## Comment 7

> Username: oracleoftroy  
> Created_at: 2018-01-06 01:34:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/351#issuecomment-355712750  

Thank you @Kojoley! It was my pleasure, and it was fun seeing all the conversations that popped up from my humble bug report and pull request.  
  
And 👍 to efforts to avoiding warnings. I'm sure it is nearly impossible with a project with the scope of boost, but libraries that compiles with warnings at reasonable warning levels is concerning.

---
