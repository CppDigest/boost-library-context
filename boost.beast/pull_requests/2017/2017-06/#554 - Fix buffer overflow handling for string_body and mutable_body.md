# #554 Fix buffer overflow handling for string_body and mutable_body [Closed]

> Username: octopus-prime  
> Created at: 2017-06-28 22:09:21 UTC  
> Updated at: 2017-06-29 01:50:33 UTC  
> Closed at: 2017-06-28 22:37:17 UTC  
> Url: https://github.com/boostorg/beast/pull/554  

issue #553

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-06-28 22:11:01 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311806865  

It needs to be all squashed down into one commit

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-28 22:12:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/554#pullrequestreview-46983054  

📁 include/beast/http/string_body.hpp

```diff
 125 |-             ec.assign(0, ec.category());
 128 |             buffer_copy(boost::asio::buffer(
 129 |                 &body_[0] + len, n), buffers);
```

> Username: vinniefalco  
> Created_at: 2017-06-28 22:12:29 UTC  
> Updated_at: 2017-06-28 22:37:17 UTC  
> Url: https://github.com/boostorg/beast/pull/554#discussion_r124670100  

if `std::distance(buffers.begin(), buffers.end()==1` then using `body_.append` is more efficient

> Username: octopus-prime  
> Created_at: 2017-06-28 22:20:17 UTC  
> Updated_at: 2017-06-28 22:37:17 UTC  
> Url: https://github.com/boostorg/beast/pull/554#discussion_r124671430  

I would always call append.  
The container should know how to handle allocation and copying at best...  
Better than we know...  
So resize() would be obsolete too. But people told me, that resize() is VERY important.  
  
Or do you mean an optimization for 1 character only?! Like push_back() ?


---

## Comment 3

> Username: octopus-prime  
> Created_at: 2017-06-28 22:14:33 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311807520  

> It needs to be all squashed down into one commit  
  
Ouch.  
Closing this pull request?  
New branch from master again.  
All changes.  
One commit.  
New pull request?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-06-28 22:23:35 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311809181  

What? No! You can squash it from the command line. Try  
```  
git remote add upstream git@github.com:vinniefalco/Beast.git  
git rebase -i upstream/master  
```  
  
or you can use   
```  
git reset 1e8be328  
git add -A  
git commit --amend  
```  
  
When you push to your remote use  
```  
git push -f  
```

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2017-06-28 22:28:40 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311810209  

Do i need  
`git add -A`  
?  
  
There are no new files to commit.  
But i have new files in workspace, e.g. `.project`

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-06-28 22:30:19 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311810481  

I don't know what `.project` is. If you used `git reset` you should have modified files in you working directory. Try resetting back to your original branch and then do the interactive rebase  (`git rebase -i`)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-06-28 22:32:11 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311810807  

See https://github.com/vinniefalco/Beast/commits/op

---

## Comment 8

> Username: octopus-prime  
> Created_at: 2017-06-28 22:39:22 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311812005  

WTH?! git push did close the pull request?! I get crazy!!

---

## Comment 9

> Username: octopus-prime  
> Created_at: 2017-06-28 22:40:35 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311812216  

okay. shit happens. i will try again tomorrow...

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2017-06-28 22:43:12 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311812695  

No worries, take your time and have patience, this will work.

---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2017-06-29 01:50:33 UTC  
> Url: https://github.com/boostorg/beast/pull/554#issuecomment-311839778  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=h1) Report  
> Merging [#554](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `57.14%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/554/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #554      +/-   ##  
==========================================  
- Coverage   93.77%   93.75%   -0.03%       
==========================================  
  Files          94       94                
  Lines        7393     7394       +1       
==========================================  
- Hits         6933     6932       -1       
- Misses        460      462       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.18% <57.14%> (-7.13%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=footer). Last update [78a065b...8982e14](https://codecov.io/gh/vinniefalco/Beast/pull/554?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
