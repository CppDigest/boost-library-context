# #98 Add Hana as a submodule of the master repository [Merged]

> Username: ldionne  
> Created at: 2015-10-19 18:12:41 UTC  
> Updated at: 2015-12-29 17:22:52 UTC  
> Merged at: 2015-12-29 16:09:22 UTC  
> Closed at: 2015-12-29 16:09:22 UTC  
> Url: https://github.com/boostorg/boost/pull/98  

Specifically:  
- Adds Hana's test suite to the regression tests  
- Add Hana as the libs/hana submodule  
- Add entry for Hana to libs/maintainers.txt and libs/libraries.htm  
  
**NOTE**  
This is not ready to be merged yet! I want to see the Travis results so I can adjust some things before this is ready.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2015-10-19 18:43:37 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-149309154  

This is missing the "libs/hana" submodule git reference.

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-10-19 18:54:56 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-149311823  

You are totally right, thanks for noticing. I also know there's a broken link in the `libs/libraries.htm` file, but I'm not sure how I should set it up. Any clue?

---

## Comment 3

> Username: ldionne  
> Created_at: 2015-10-19 20:43:20 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-149341529  

@grafikrobot The Travis builds seem to be stalled, any idea why that is?

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-12-29 16:05:38 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167820953  

@grafikrobot Is there anything else to do to ensure that the submodule is updated on the master repository every time I push to Hana's repository?  
  
Otherwise, we should be good to go.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2015-12-29 16:09:57 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167821808  

Thanks for the reminder.

---

## Comment 6

> Username: ldionne  
> Created_at: 2015-12-29 16:11:27 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167822214  

It was a genuine question more than a reminder. I assume that we're good to go, then. Thanks!

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2015-12-29 16:21:48 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167824481  

Yeah, it's good to go.. Assuming there are no problems with this just merged commit. I.e. that the tests do start showing up in the results. I'll need another PR for merging this into master.

---

## Comment 8

> Username: ldionne  
> Created_at: 2015-12-29 17:01:27 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167830615  

> I'll need another PR for merging this into master.  
  
Wait.. In this PR, the `libs/hana` submodule points to Hana's `master` branch. I just assumed that everything would be handled automatically, but this is probably wrong given your comment.  
  
Do I understand correctly that I need:  
- a PR against `develop` like the one I just did, but with the `libs/hana` submodule pointing to Hana's `develop` branch  
- an identical PR against `master`, except the `libs/hana` submodule is pointing to Hana's `master` branch

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2015-12-29 17:06:32 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167831562  

On Tue, Dec 29, 2015 at 11:01 AM, Louis Dionne notifications@github.com  
wrote:  
  
> I'll need another PR for merging this into master.  
>   
> Wait.. In this PR, the libs/hana submodule points to Hana's master  
> branch. I just assumed that everything would be handled automatically, but  
> this is probably wrong given your comment.  
>   
> Yeah, didn't notice the SHA was pointing to master. And that SHA will get  
> overwritten by the automatic master project updater anyway.  
>   
> Do I understand correctly that I need:  
> - a PR against develop like the one I just did, but with the libs/hana  
>   submodule pointing to Hana's develop branch  
>   
> No. See above comment. As soon as you make a change to the Hana develop it  
> will get repointed correctly.  
> - an identical PR against master, except the libs/hana submodule is  
>   pointing to Hana's master branch  
>   
> Yes. Preferably by cherry-picking the same changes here but into the  
> master branch.  
  
##   
  
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---

## Comment 10

> Username: ldionne  
> Created_at: 2015-12-29 17:22:52 UTC  
> Url: https://github.com/boostorg/boost/pull/98#issuecomment-167834546  

Ah, now I understand better. Thanks, I'll test that the integration works properly with develop and then I'll submit a PR for master.

---
