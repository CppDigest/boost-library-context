# #402 - Why isn't boost-python getting bumped? [Open]

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2022-11-10 21:14:55 UTC  
> Updated at: 2022-11-23 10:19:54 UTC  
> Url: https://github.com/boostorg/python/issues/402  

The submodule for boost-python is pinned to https://github.com/boostorg/python/tree/8dd151177374dbf0aa5cb86bd350cf1ad13e2160. As of writing that commit is 18 months old.  
  
It looks like the submodule hasn't been bumped since boost 1.77.0.  
  
There have been a number of commits to its default branch since that pinned commit. In particular, https://github.com/boostorg/python/pull/385 fixes boost-python being broken in cpython 3.11, which is blocking many users (https://github.com/boostorg/python/issues/400, https://github.com/boostorg/python/issues/388, https://github.com/arvidn/libtorrent/discussions/7178)  
  
What needs to be done to get this change into a released version of boost?

---

## Comment 1

> Username: userdocs  
> Created at: 2022-11-10 22:15:20 UTC  
> Updated at: 2022-11-10 22:15:46 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1315938968  

I think, and i don't really understand how this works, that boost-python created multiple tags for that commit on the same date of Jul 4, 2021.   
  
Looking here: https://github.com/boostorg/python/tags we can see all these tags including 1.80.0 share this commit  
  
```  
git ls-remote -q -t --refs https://github.com/boostorg/python.git | grep 8dd151177374dbf0aa5cb86bd350cf1ad13e2160  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.77.0  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.77.0.beta1  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.78.0  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.78.0.beta1  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.79.0  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.79.0.beta1  
8dd151177374dbf0aa5cb86bd350cf1ad13e2160        refs/tags/boost-1.80.0  
```  
  
I am going to guess this is part of the reason why the link does not get bumped here? Unless i just understanding this wrong but it appears certain submodules are tagged and then bumped accordingly. For example.  
  
https://github.com/boostorg/thread/tree/743d19d7d337af9705a882f55907e3b7622514b3  
  
bumped 4 months ago. I am pretty sure this is relevant to why boost python is no updated?  
  
That boost-python would need to redo the tag, essentially.

---

## Comment 2

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2022-11-10 22:25:12 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1315938977  

I don't believe the tags in a library are authoritative for how the library gets pulled into the megaproject. Rather it looks like libraries get pulled from their main branches regularly by CI: https://github.com/boostorg/boost/commits?author=boost-commitbot  
  
I presume the tags in the library descend from megaproject releases, not the other way around.  
  
Actually, maybe this is as simple as megaproject CI pulling from the wrong branch. It looks like boost-python was last pulled in https://github.com/boostorg/boost/commit/eb4a8fca7a928aa1b78283f591383fb70a27360a (`Update python from master`). Indeed https://github.com/boostorg/python/tree/master is at the old commit. However boost-python's default branch is named `develop`, not sure when that may have changed.

---

## Comment 3

> Username: userdocs  
> Created at: 2022-11-10 22:32:00 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1315938988  

I am not sure how this is working but this seems to show the bot is responding to the submodule?  
  
![image](https://user-images.githubusercontent.com/16525024/201219790-3692b66c-e95d-47d0-9b8e-c6b140a8af37.png)  
  
All tagged from this one commit  
  
https://github.com/boostorg/python/commit/8dd151177374dbf0aa5cb86bd350cf1ad13e2160  
  
As i said, i'm just guessing but it seems possible to be related.

---

## Comment 4

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2022-11-10 22:46:41 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1315938996  

I think `boost-1.80.0` and so on are lightweight tags, which have no author or date of their own. They just point to a commit. The UI is misleading to say "(user) tagged this (date)" in this case. I can't find a conclusive way to test whether a commit is lightweight or annotated though, except that `git show` etc don't *seem* to show annotations.  
  
(Obviously the author didn't create a bunch of tags for future boost versions up to today's, on the same day they created the commit 18 months ago)  
  
As a lightweight tag, `boost-1.80.0` has no authorship or history to figure out who created it. I'm just guessing it was created by CI given the other context.

---

## Comment 5

> Username: userdocs  
> Created at: 2022-11-11 12:40:25 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1315939002  

https://github.com/boostorg/python/commit/909a4d1530f3e826c7e45e945cde65069291da11  
  
Maybe it requires a merge from develop into master as this is what appears to have happened last time.  
  
@stefanseefeld can you please explain how this works as we need the merged commit from develop?

---

## Comment 6

> Username: jakobandersen  
> Created at: 2022-11-22 20:17:27 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1324192813  

> There have been a number of commits to its default branch since that pinned commit. In particular, #385 fixes boost-python being broken in cpython 3.11, which is blocking many users (#400, #388, [arvidn/libtorrent#7178](https://github.com/arvidn/libtorrent/discussions/7178))  
  
With Homebrew on macOS it seems that their boost-python package is already depending on Python 3.11 (https://formulae.brew.sh/formula/boost-python3), meaning there is no easy way (if any?) to use it if one has enums. Getting this into 1.81 would give Homebrew a way to upgrade into a working state.

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2022-11-22 20:20:08 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1324195090  

I did merge the `develop` branch to `master` a few days ago, so this should be part of the upcoming `1.81.0` release.

---

## Comment 8

> Username: jakobandersen  
> Created at: 2022-11-23 10:19:54 UTC  
> Url: https://github.com/boostorg/python/issues/402#issuecomment-1324830473  

@stefanseefeld, thanks!
