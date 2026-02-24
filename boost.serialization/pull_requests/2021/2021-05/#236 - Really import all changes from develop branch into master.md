# #236 Really import all changes from develop branch into master [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2021-05-14 15:34:52 UTC  
> Updated at: 2023-08-24 17:44:20 UTC  
> Closed at: 2022-09-15 23:03:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/236  

Hi @robertramey,  
  
It seems you don't use any "git merge" strategy to merge changes from develop to master branch. It happens that this strategy seems to have lead to missing at least this commit https://github.com/boostorg/serialization/commit/07c99b8649d7bb900271ca93ba3eea9dcb478f42 on master branch. Checking the diff between master and develop following your recent's "squash merge all changes in develop into master" commit in early may, I have found that actually there are quite some differences elsewhere between the two branches (especially in tests). Is it intended that we have such differences ? Naively, without knowing this project at all, I would be tempted to say no, but I may of course be wrong.  
  
So I decided to submit this pull request which would really align code base on both branch. This was generated using this command (on the branch master, and on develop branch I had cherry-picked commits from both #PR 233 and #PR 235 as I think these are useless files):  
  
    git apply --index <(git diff --binary master develop) && git commit -m "Really import all changes from develop branch into master."  
  
I hope that's ok.  
  
Cheers,  
Romain

---

## Comment 1

> Username: robertramey  
> Created_at: 2021-05-14 18:00:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-841410036  

lately I've been using "squash merge" on my "merges" into master branch from development.  This has the following effects:  
  
1.  Develop branch is unaffected as usual  
2. the history in the develop branch are "squashed" - that is consolidated into one change for file.  So now I have what amounts to two separate histories:  the develop branch which contains all the changes including all the fixups on all files.  So it contains a lot of "cruft" that the world never needs to see.  
3. the history on the master branch contains only changes from one release to another.  Not using "squash" I get all the cruft on the develop branch injected into the history on the master branch.  
4. put another way: Development history is on develop branch and Release history is on the "master" branch.  
  
Questions have been raised about whether this is a good idea.  I like the "two histories" aspect, but it DOES feel a little weird.  I don't have a strong commitment to this method so I'm open to considering it if I get some substantive complaints.  The jury on this is out for now.  
  
Note that git is a lot more complex than meets the eye and I for one have had problems in using it - especially in the odd cases.  I generally use SourceTree to use it.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2021-05-14 18:19:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-841419838  

My personal opinion is that double history is a bad idea (ie more than just weird as you wrote it), and if people would start using such scheme in my company I would definitely reject it. But that's my opinion only, it doesn't really count as I am not contributing to this library.  
  
You are free to use whatever schemes suits you and your users. However IMO if you do use this double history, it becomes quite important that you can ensure easily both branch won't diverge over time. Apparently here there was divergence at least these last months. I never use any UI for git because this is definitely not my thing at all, so I can't give you tips on how do it correctly with SourceTree. However I advise you to regularly do some diff between both branches (I guess SourceTree allows that), to make sure you have none at each "squash merge" points.

---

## Comment 3

> Username: robertramey  
> Created_at: 2021-05-14 18:28:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-841423736  

Right - opinions can differ on this.  As I said, I'm still evaluating it.  
  
FYI, nothing is ever checked into the master.  Note in the past this accidentally happened from time to time and created problems. It is only used as the target of a full "squash merge" So, unless someone screws up, it should always be in sync with the develop branch at the point of the last release.  
  
I'm still evaluating this.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2021-08-05 15:31:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-893554019  

@robertramey What shall we do with this pull request ? Boost 1.77 is about to be released, and still the code which will be shipped in this release is not the code you test in the develop branch.  
  
I have just rebased on top of the latest "master" branch, importing the latest "develop" changes. After this both branch are identical (except for the empty b2.log file).

---

## Comment 5

> Username: robertramey  
> Created_at: 2021-08-07 17:41:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-894683939  

OK I'm pretty confused now.  
  
a) I can't permit people to check in changes to the the master branch - or even the develop branch for that matter.   
b) I need to have changes submitted as a PR  
c) I only now (I think) looked at all the changes proposed.  I'm still evaluating them.  
d) I believe that all the bogus b2.log file have been removed from develop.  Before I do the next release, they should be removed from the master via the process which merges the develop changes into the master.  
  
I'll look again at the other changes.

---

## Comment 6

> Username: Romain-Geissler-1A  
> Created_at: 2021-08-09 23:38:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-895622446  

Hi,  
  
I honesty don't understand the point a) and c) in the scope of this particular pull request proposal.  
  
Again, I am not trying to create any new development targetting only branch "master". I am only trying to fix past "merge" (you don't exactly use merges, but let's call what you do "merges" anyway) from develop to master which apparently failed to propagate *ALL* past changes. It's been some time you *think* branch "master" contains the same code as the "develop" branch at "merge" points, but really it doesn't. What is shipped since quite some time in official Boost release tarballs *IS NOT* what you test and what you think is shipped.  
  
This pull request only tries to fix that, and really make sure the code in "master" really is the same as the code in the "develop" branch. Nothing else. There is *NO* new code, only things you did approve in the past already and which wasn't properly propagated to the "master" branch quite some time ago. So I am not sure why now you want to re-evaluate what you approved already in the branch develop.  
  
The only thing I am doing it is blindly using git (from the command line, and not from a UI like it seems you use it), without trying to even understand the underlying git, so that in the end both "develop" and "master" branch contain the same code. In particular I wish the file include/boost/serialization/collection_size_type copy.hpp in master branch is deleted, as it was in branch develop already, as in my company it creates errors when our IDE tries to parse it, while this file isn't actually useful.  
  
Cheers,  
Romain

---

## Comment 7

> Username: robertramey  
> Created_at: 2022-09-15 21:19:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-1248642717  

What is the current status on this?  Is it still an issue?  This "extra file" shouldn't appear in either the master or develop branches.

---

## Comment 8

> Username: Romain-Geissler-1A  
> Created_at: 2022-09-15 23:03:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-1248732445  

Indeed the file is gone on master branch, so I guess now the boost releases also have the fix since quite some time.  
  
I still think you have an original way to use git, which IMO is very error prone, but you are the maintainer, so it's your choice ! ;)  
  
I am closing this pull request.

---

## Comment 9

> Username: Romain-Geissler-1A  
> Created_at: 2022-12-12 16:45:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-1346857256  

Hi @robertramey,  
  
Checking right now the "master" branch (as well as the beta tag for 1.81), it seems the last commit on master branch is from 2021. And the file https://github.com/boostorg/serialization/blob/master/include/boost/serialization/collection_size_type%20copy.hpp is still present while it should have been removed. So have been gone back in time compared to the state of master on 16th of September ?  
  
Cheers,  
Romain

---

## Comment 10

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-15 22:42:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-1679722363  

@robertramey Ping

---

## Comment 11

> Username: robertramey  
> Created_at: 2023-08-24 17:44:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/236#issuecomment-1692153926  

I've made changes to the develop branch which are now passing tests.  Next time I merge from develop to master, I'd quadruple check that that file isn't there.  That should do it!

---
