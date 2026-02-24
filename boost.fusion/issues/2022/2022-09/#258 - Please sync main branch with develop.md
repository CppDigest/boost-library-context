# #258 - Please sync main branch with develop [Closed]

> Username: Kojoley  
> Created at: 2022-09-25 20:17:19 UTC  
> Updated at: 2022-09-27 05:28:14 UTC  
> Closed at: 2022-09-26 17:35:22 UTC  
> Url: https://github.com/boostorg/fusion/issues/258  

The branches diverged and contain disjoint commits.  
![image](https://user-images.githubusercontent.com/2743474/192163354-786efb48-03a0-4817-b12e-ba8818712a49.png)

---

## Comment 1

> Username: djowel  
> Created at: 2022-09-26 00:17:27 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257320016  

master and develop are synced now.

---

## Comment 2

> Username: Kojoley  
> Created at: 2022-09-26 00:23:54 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257322243  

GitHub still shows  
![image](https://user-images.githubusercontent.com/2743474/192172654-5dd3572f-247b-4d77-a3e4-790813419744.png)  
  
  
Differences:  
https://github.com/boostorg/fusion/compare/develop...master  
https://github.com/boostorg/fusion/compare/master...develop

---

## Comment 3

> Username: djowel  
> Created at: 2022-09-26 01:25:12 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257353154  

Just to be sure I'm not messing up something:  
https://github.com/boostorg/fusion/pull/259

---

## Comment 4

> Username: Kojoley  
> Created at: 2022-09-26 01:56:58 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257373913  

I think you need to merge master to develop, then merge develop to master, and then you will be able to fast-merge master to develop, after which branches will be equal.

---

## Comment 5

> Username: djowel  
> Created at: 2022-09-26 02:01:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257376874  

Alright, let me do this again. Pardon my confusion!

---

## Comment 6

> Username: djowel  
> Created at: 2022-09-26 04:39:05 UTC  
> Updated at: 2022-09-26 04:39:52 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257471434  

Here was the issue after pushing (with fast-merging master to develop):  
  
```  
~/dev/boost/libs/fusion> git push --porcelain --progress --recurse-submodules=check origin refs/heads/develop:refs/heads/develop  
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0  
remote: error: GH006: Protected branch update failed for refs/heads/develop.          
remote: error: This branch must not contain merge commits.          
To github.com:boostorg/fusion.git  
! refs/heads/develop:refs/heads/develop [remote rejected] (protected branch hook declined)  
failed to push some refs to 'github.com:boostorg/fusion.git'  
Done  
'develop' rejected (non-fast-forward)  
```  
  
I temporarily disabled GitHub's "Require linear history" rule on protected branches (develop)  
(Prevent merge commits from being pushed to matching branches.)  
  
It's now in sync. Next time, I'll enforce always merging PRs to `develop` to avoid this mess. Some PRs were merged to `master`.  
  
Please check the state if I messed up anything.

---

## Comment 7

> Username: denzor200  
> Created at: 2022-09-26 05:30:02 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257500752  

Please, dont forget about this: https://github.com/boostorg/fusion/pull/246.  
I seen that these troubles fixed here https://github.com/boostorg/fusion/commit/a8847092537edc38dcf28704076ae3b8cd2cc20c, but it's not enough

---

## Comment 8

> Username: djowel  
> Created at: 2022-09-26 06:03:46 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1257521799  

> Please, dont forget about this: #246. I seen that these troubles fixed here [a884709](https://github.com/boostorg/fusion/commit/a8847092537edc38dcf28704076ae3b8cd2cc20c), but it's not enough  
  
Merged.

---

## Comment 9

> Username: Kojoley  
> Created at: 2022-09-26 17:35:22 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1258386115  

> It's now in sync. Next time, I'll enforce always merging PRs to `develop` to avoid this mess. Some PRs were merged to `master`.  
>   
> Please check the state if I messed up anything.  
  
The history looks quite sad with duplicated commits, but it works. Now develop is superset of master branch.  
  
It is indeed much less gymnastics and pain when you don't create commits in master which have not land in develop, I also learned that the hard way. It is also sad that GitHub UI buttons don't give you a choice to create a fast-forward merge when you want to sync master with develop, though generally it is a good idea to always use git pull/merge with `--ff-only` flag unless you really mean to create a merge commit. Btw, one haven't messed up until they push local changes to the repository server.

---

## Comment 10

> Username: denzor200  
> Created at: 2022-09-27 03:55:24 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1258935857  

https://github.com/boostorg/fusion/pull/246 still wasn't merged to master.

---

## Comment 11

> Username: djowel  
> Created at: 2022-09-27 04:10:38 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1258944249  

> #246 still wasn't merged to master.  
  
Is it a priority? Normally we let code sit on develop for a while to minimize disruptions on master.

---

## Comment 12

> Username: denzor200  
> Created at: 2022-09-27 05:28:14 UTC  
> Url: https://github.com/boostorg/fusion/issues/258#issuecomment-1258996913  

> Is it a priority? Normally we let code sit on develop for a while to minimize disruptions on master.  
  
It's just a small fix for possible compile-time error.
