# #158 ndarray.shape,strides act more like their python counterparts see #157 [Closed]

> Username: mborgerding  
> Created at: 2017-09-20 13:45:07 UTC  
> Updated at: 2017-09-20 21:14:37 UTC  
> Closed at: 2017-09-20 21:11:29 UTC  
> Url: https://github.com/boostorg/python/pull/158  

addresses #157  
Unit tests added for to verify correct behavior for shape(k),strides(k) with negative indexing.  Verify IndexError raised when k out of bounds.

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2017-09-20 20:54:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/158#pullrequestreview-64113243  

The change itself looks good, but it shouldn't be lumped into the PR about negative strides and indices. (For avoidance of doubt: I'm talking about the second commit only. The first is fine, and only awaiting a completed test run on travis.)

---

## Comment 2

> Username: mborgerding  
> Created_at: 2017-09-20 20:59:15 UTC  
> Url: https://github.com/boostorg/python/pull/158#issuecomment-330979111  

@stefanseefeld , I agree the changes shouldn't be lumped together.  That was not my intent, but github did it on its own (I may've missed something) .  If you feel they should be separated, I can try again, maybe rebasing both changes from the master branch would force github to make them separate PRs.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2017-09-20 21:01:57 UTC  
> Url: https://github.com/boostorg/python/pull/158#issuecomment-330979879  

Yeah, simply move the second one into a separate branch from which you can submit another PR. Sorry it's all a bit formal, but with practice it becomes easier ;-)

---

## Comment 4

> Username: mborgerding  
> Created_at: 2017-09-20 21:11:29 UTC  
> Url: https://github.com/boostorg/python/pull/158#issuecomment-330982253  

Split this into two PRs. From now on I'll know not to believe that github's "Create New Pull Request" will actually create a _New_ pull request.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2017-09-20 21:14:37 UTC  
> Url: https://github.com/boostorg/python/pull/158#issuecomment-330983056  

FWIW, you could simply have added another commit reverting the previous one (which I could have squash-and-merged as one - so only the first one would have been visible), or you could have force-pushed an update removing the second commit.  
But of course, starting over with a new PR works, too. :-)

---
