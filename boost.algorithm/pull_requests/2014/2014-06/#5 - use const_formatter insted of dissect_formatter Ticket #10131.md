# #5 use const_formatter insted of dissect_formatter  Ticket #10131 [Closed]

> Username: ghost  
> Created at: 2014-06-18 09:56:53 UTC  
> Updated at: 2019-07-03 15:02:22 UTC  
> Closed at: 2019-07-03 15:02:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5  

reference to Ticket #10131  
  
This may be a too naive solution to the problem I described in Ticket #10131, as I do not yet fully understand the concept behind the formatters.  
  
This is my reasoning:  
  
The trim_all___if() functions use the dissect_formatter with the first element as the argument. Since we are actually just trimming *all_ I doubt that it is necessary to keep the "head". Since I found the trim_fill_*_if() functions with an empty replacement string to work correctly, we could use the same approach here. I've replaced the dissect_formatter with the const_formatter taking an empty string argument.   
  
I have found this working with my code and examples. But as I said above, since I do not yet grasp the full concept of the formatters, there could be side effects I was not able to detect.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-06-18 11:38:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-46424536  

This is the wrong place to merge these changes.  
They should go into https://github.com/boostorg/algorithm:develop.  
Then, after the tests cycle (and we verify) that there's no problems on all platforms, we can merge to algorithm:master (which will get swept into boost::master)  
  
That being said - thanks for doing this.  
The changes to the docs and to hex.hpp are uncontroversial, but the one to  string/trim_all.hpp will require a bit of study. One thing - don't leave the old (incorrect code) in the file but commented out. Please remove them.

---

## Comment 2

> Username: ghost  
> Created_at: 2014-06-20 17:20:55 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-46703751  

Am Mittwoch, 18. Juni 2014, 04.38:40 schrieb Marshall Clow:  
  
> This is the wrong place to merge these changes.  
> They should go into https://github.com/boostorg/algorithm:develop.  
> Then, after the tests cycle (and we verify) that there's no problems on all  
> platforms, we can merge to algorithm:master (which will get swept into  
> boost::master)  
> Thanks for pointing this out. This makes sense and is pretty much what I   
> wanted. I'm pretty new to the boost development process.   
>   
> That being said - thanks for doing this.  
> The changes to the docs and to hex.hpp are uncontroversial, but the one to   
> string/trim_all.hpp will require a bit of study. One thing - don't leave  
> the old (incorrect code) in the file but commented out. Please remove them.  
> Will do so.  
  
regards Willi ...

---

## Comment 3

> Username: mclow  
> Created_at: 2014-06-20 20:11:41 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-46721380  

Checking the develop branch, it appears that the first two fixes have already been made; leaving only the one for the bug that you filed.

---

## Comment 4

> Username: zamazan4ik  
> Created_at: 2017-05-04 05:36:36 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-299100389  

@mclow what is the current status of this PR?

---

## Comment 5

> Username: mclow  
> Created_at: 2017-05-04 13:52:56 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-299191434  

this PR will never be merged - because it is against the master branch, not develop. However, there are a couple of things that I here might want to cherry-pick into the develop branch.

---

## Comment 6

> Username: zamazan4ik  
> Created_at: 2017-05-04 13:58:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-299192967  

What do you want to cherry-pick to the develop branch from this PR? Maybe i can prepare valid PR to the develop branch (or maybe will be better if you will do it).

---

## Comment 7

> Username: mclow  
> Created_at: 2017-05-04 14:14:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-299197613  

I'll do it.

---

## Comment 8

> Username: zamazan4ik  
> Created_at: 2017-07-06 23:06:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-313542991  

Did you cherry-pick things from the PR?

---

## Comment 9

> Username: mclow  
> Created_at: 2019-07-03 15:02:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/5#issuecomment-508131745  

The docs and the `hex` change have been merged. The change to trim broke a bunch of tests, so I'm not going to merge that.

---
