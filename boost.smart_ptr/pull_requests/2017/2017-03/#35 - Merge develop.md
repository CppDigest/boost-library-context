# #35 Merge develop [Merged]

> Username: glenfe  
> Created at: 2017-03-03 14:57:53 UTC  
> Updated at: 2017-03-03 18:23:36 UTC  
> Merged at: 2017-03-03 18:23:36 UTC  
> Closed at: 2017-03-03 18:23:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/35  

Pull request to trigger all compilers tests

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-03-03 15:37:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/35#issuecomment-283986190  

Every push to develop is automatically tested, so there's no need to trigger that with a PR, although it doesn't hurt. You can see the results in  
  
https://github.com/boostorg/smart_ptr/commits/develop

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-03-03 16:05:42 UTC  
> Updated_at: 2017-03-03 16:09:14 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/35#issuecomment-283994183  

Although on second thought this tests master, not develop, so it's not the same.  
  
On third thought, the commit SHA is the same as the tip of develop, which seems to confuse the CI integration and AppVeyor doesn't seem to run. You probably merged without --no-ff.

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-03-03 17:53:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/35#issuecomment-284023434  

That's interesting. I created this pull request via the github.com user interface which, I think, should be a non-fast-forward merge according to [this](https://help.github.com/articles/merging-a-pull-request/).

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-03-03 18:04:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/35#issuecomment-284026325  

That's however about merging the pull request.  
  
Things are of course a bit tricky when the PR consists of a merge.  
  
Anyway, Travis did run, so you could just test locally on however many MSVC versions you have and then push.

---
