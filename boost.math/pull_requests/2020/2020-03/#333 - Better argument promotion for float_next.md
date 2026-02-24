# #333 Better argument promotion for float_next [Merged]

> Username: NAThompson  
> Created at: 2020-03-28 21:06:54 UTC  
> Updated at: 2020-04-10 16:24:15 UTC  
> Merged at: 2020-04-10 10:52:43 UTC  
> Closed at: 2020-04-10 10:52:43 UTC  
> Url: https://github.com/boostorg/math/pull/333  

For some reason I couldn't get the unit tests for this to compile even without my diffs, so I'm flying a bit blind here.  
  
When finished, should fix #332.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-03-29 14:46:27 UTC  
> Updated_at: 2020-03-29 17:24:12 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-605647908  

@jzmaddock : What's the opinion on two integral types? Error or double promotion?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-03-29 17:28:15 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-605670813  

>What's the opinion on two integral types? Error or double promotion?  
  
IMO it doesn't make any sense, I would say error.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-03-29 22:01:26 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-605708569  

@jzmaddock : Looks like it's failing due to use of C++11 features. Since C++03 is deprecated, should we remove those build jobs?

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-03-30 00:06:10 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-605723285  

Just wanted to see if everything would go green; we can revert that commit if need be.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-03-30 08:06:36 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-605847198  

>Looks like it's failing due to use of C++11 features. Since C++03 is deprecated, should we remove those build jobs?  
  
Sadly not yet: deprecated means "we will remove support in future" not that we've removed it right now.  So we're committed to continuing C++03 support till next year :(

---

## Comment 6

> Username: NAThompson  
> Created_at: 2020-04-06 09:22:01 UTC  
> Updated_at: 2020-04-06 09:28:18 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-609677680  

@jzmaddock : I'm somewhat worried that this change is a de facto removal of C++03 support. `next.hpp` is used pervasively; see [this build](https://travis-ci.org/github/boostorg/math/jobs/671225659).  
  
Basically I'm having trouble getting a green build without slapping `[ requires cxx11_numeric_limits cxx11_hdr_tuple cxx11_auto_declarations ] ` on every target . . .

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-04-06 17:42:02 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-609938131  

> I'm somewhat worried that this change is a de facto removal of C++03 support. next.hpp is used pervasively;  
  
I don't think we should do that - not till this time next year anyway - give me a moment and I'll see if I can back-port to C++03.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2020-04-10 13:05:16 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-612019843  

Will this fix go into the release?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2020-04-10 16:21:36 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-612105220  

It's too late for the beta, and since it's not really a bug fix, I think it's too late really?

---

## Comment 10

> Username: NAThompson  
> Created_at: 2020-04-10 16:24:15 UTC  
> Url: https://github.com/boostorg/math/pull/333#issuecomment-612106522  

I guess it's a question of definition as to whether its a bugfix. I say it is, since my mental model of how it should work has been broken a few times, and I've gotten incorrect answers.  
  
At very least, it's an abstraction leak.

---
