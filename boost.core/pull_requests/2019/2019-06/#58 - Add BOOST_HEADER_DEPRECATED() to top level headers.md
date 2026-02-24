# #58 Add BOOST_HEADER_DEPRECATED() to top level headers [Closed]

> Username: hazelnusse  
> Created at: 2019-06-02 22:22:54 UTC  
> Updated at: 2022-05-13 22:45:00 UTC  
> Closed at: 2022-05-13 22:44:59 UTC  
> Url: https://github.com/boostorg/core/pull/58  



---

## Comment 1

> Username: pdimov  
> Created_at: 2019-06-03 00:09:43 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498077151  

Not a bad idea overall, but maybe not do all at once. We can start with the ones in `detail` and `boost/iterator.hpp` for now.

---

## Comment 2

> Username: hazelnusse  
> Created_at: 2019-06-03 00:19:45 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498077972  

If the build passes, what is the downside of doing it all now? Won't this just result in warning messages being displayed to those who aren't compiling with `BOOST_ALLOW_DEPRECATED_HEADERS`? For those who have warnings as errors, this would break their builds but everybody else would just see a bit more output.  
If you feel strongly about it, I'll defer to your judgement and update the commit to change only the ones in `detail` and `boost/iterator.hpp`.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-06-03 00:31:30 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498079041  

People think of Boost as a single thing and when "we" start emitting warnings without fault of theirs, they complain. Now in this case it won't be us, as in Boost.Core, that will be responsible for the warning, it will be another Boost library that includes the deprecated header. But it won't matter, the warning comes from Boost, and that's that.  
  
So it's better to proceed more carefully and first deprecate some headers, then fix their uses throughout Boost, then repeat.

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-06-03 00:40:17 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498079745  

Also, the PR should fix the uses of the deprecated headers in Core itself; our own tests shouldn't emit deprecation warnings. :-)  
  
https://travis-ci.org/boostorg/core/jobs/540511632

---

## Comment 5

> Username: hazelnusse  
> Created_at: 2019-06-03 02:40:15 UTC  
> Updated_at: 2019-06-03 02:40:36 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498094403  

I'm working on removing all references to the deprecated headers from within Boost.Core.  
  
I first noticed in Boost 1.70.0 the BOOST_HEADER_DEPRECATED() that was added by you in this commit:  
https://github.com/boostorg/timer/commit/075d6b25ab185526b0704fc9d1c982931729c35c  
  
Personally, I appreciated this warning and took the time to remove our codebase's use of the deprecated header.  
  
What is the reasoning you used when deciding to add this warning to Boost.Timer and do you believe that reasoning doesn't apply equally to Boost.Core?

---

## Comment 6

> Username: hazelnusse  
> Created_at: 2019-06-03 05:28:22 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498117634  

@pdimov I've update the PR to include two separate commits. The first removes (I think) all uses of the deprecated headers. The second adds `BOOST_HEADER_DEPRECATED` to all the deprecated files. If you'd prefer, I can break the second commit into two, one which makes the change to the files you suggested, and the other which changes the rest -- which perhaps we can remove from this PR if there are good reasons to keep that macro out of those files for now.

---

## Comment 7

> Username: hazelnusse  
> Created_at: 2019-06-03 05:29:05 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-498117756  

I also took the liberty to do some alpha-sorting on the headers, hopefully that isn't too disruptive.

---

## Comment 8

> Username: Lastique  
> Created_at: 2021-09-05 23:42:18 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-913248215  

I don't think this PR is actionable as is. We have marked some headers deprecated, and there probably were other changes that caused conflicts. Also, I'm not a fan of alpha-sorting headers as that particular order is useless.  
  
I propose to close this PR. We can continue marking headers deprecated in a piecemeal manner, possibly in separate PRs.

---

## Comment 9

> Username: Lastique  
> Created_at: 2022-05-13 22:44:59 UTC  
> Url: https://github.com/boostorg/core/pull/58#issuecomment-1126559049  

No feedback, so I'll close this. Not because the idea is bad, but because the PR is not actionable as is.

---
