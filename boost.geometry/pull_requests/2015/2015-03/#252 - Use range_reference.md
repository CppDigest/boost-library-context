# #252 [range] Use range_reference [Merged]

> Username: sdebionne  
> Created at: 2015-03-09 08:21:26 UTC  
> Updated at: 2015-03-09 12:35:40 UTC  
> Merged at: 2015-03-09 11:58:59 UTC  
> Closed at: 2015-03-09 11:58:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/252  

This PR fixes `range::at()`, `range::front()` and `range::back()` with Ranges (such as `iterator_range<>`) that does not handle constness like containers.  
This PR is a followup of [PR#245](https://github.com/boostorg/geometry/pull/245).

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-09 08:31:31 UTC  
> Updated_at: 2015-03-09 08:52:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#discussion_r26020258  

Please remove the leading spaces in the two lines above.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-09 08:31:50 UTC  
> Updated_at: 2015-03-09 08:52:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#discussion_r26020268  

Please remove the leading dashes in the three line above.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2015-03-09 08:35:58 UTC  
> Updated_at: 2015-03-09 08:52:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#discussion_r26020427  

OK, I have one git question: shall I amend the commit (like I just did to adjust the comment) or make a new commit with the removed spaces ?

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-09 08:41:52 UTC  
> Updated_at: 2015-03-09 08:52:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#discussion_r26020647  

I still see only one commit in this PR.  
  
As far as I understand `git commit --amend` works for the tip of the tree. So, since you have a single commit you can certainly use it in your local branch to fix things in the commit message.  
To actually redo the commit, you probably need to do `git reset <some hash>` or `git reset <some reflog>`, do the modifications and redo the commit.  
All these are okay with your local branch, but I have no experience as to how they play along with pull requests.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2015-03-09 08:58:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#discussion_r26021375  

I have done a `push --force` so that the PR remains a single simple commit (not three commits with space removed). It's bad practice, but I don't think anyone has pulled my branch in the latest 5 minutes... so it should be armless.

---

## Comment 6

> Username: sdebionne  
> Created_at: 2015-03-09 09:01:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#issuecomment-77819184  

@mkaravel I have removed all the spaces that were added by mistake. Sorry about that.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-03-09 11:58:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/252#issuecomment-77840951  

Thanks for this!

---
