# #665 Add typo fix to master [Merged]

> Username: striezel  
> Created at: 2022-05-07 22:18:42 UTC  
> Updated at: 2022-05-14 21:02:12 UTC  
> Merged at: 2022-05-08 20:59:57 UTC  
> Closed at: 2022-05-08 20:59:57 UTC  
> Url: https://github.com/boostorg/gil/pull/665  

### Description  
  
This PR adds the changes of #521 to the master branch. It also includes updated release notes. (I hope I got the format right.)  
  
### References  
  
See #521 for the original changes.  
  
Since the commit f6a35532f05963804c83f6519955c6fd283a3978 of this PR is a commit from develop, please do _not_ rebase or squash commits when merging. Commit f6a35532 should stay as it is. The intention is to keep master closer to develop, not duplicating commits from develop on master.  
  
### Tasklist  
  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-05-07 22:20:39 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120307106  

Sorry for the additional merge commit, but that was required to resolve a merge conflict.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-05-07 22:30:49 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120309954  

Something did not work during the conflict resolution as the diff markers make it in to the content, innit?

---

## Comment 3

> Username: striezel  
> Created_at: 2022-05-07 22:46:26 UTC  
> Updated_at: 2022-05-07 22:47:11 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120311713  

What do you mean? I do not see any diff markers here on my end.  
  
But if it is a problem, then I can remove the merge commit.

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-05-07 22:53:04 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120312400  

My apologies, mobile GitHub app made fun of me.

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-05-07 22:54:30 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120312535  

There seem to be merge conflict though

---

## Comment 6

> Username: striezel  
> Created_at: 2022-05-07 22:55:00 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120312587  

> My apologies, mobile GitHub app made fun of me.  
  
Ah, I see.  
  
In the meantime I've removed the merge commit via force push. (Was commit e06b9da0d866b32aa0b6fbf9c0d05bbefadd1778.) Shall I restore it? Or do you want to handle it?

---

## Comment 7

> Username: striezel  
> Created_at: 2022-05-07 22:56:27 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120312737  

> There seem to be merge conflict though  
  
Bad timing on my side. I just remove the merge commit, but restored it now. There should be no conflict now.

---

## Comment 8

> Username: mloskot  
> Created_at: 2022-05-08 21:00:20 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1120486194  

Thanks

---

## Comment 9

> Username: mloskot  
> Created_at: 2022-05-12 20:27:19 UTC  
> Url: https://github.com/boostorg/gil/pull/665#issuecomment-1125393056  

As I mentioned in my comment reviewing this PR https://github.com/boostorg/gil/pull/670#pullrequestreview-971415430, this change should be reverted as we don't include in `RELEASES.md` any changes related to CI, configurations or other trivial chores.  
  
I'm sorry, this merge was my mistake.

---
