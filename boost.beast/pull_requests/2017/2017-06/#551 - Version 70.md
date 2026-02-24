# #551 Version 70 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-28 04:07:27 UTC  
> Updated at: 2017-06-28 22:08:29 UTC  
> Merged at: 2017-06-28 21:13:57 UTC  
> Closed at: 2017-06-28 21:13:57 UTC  
> Url: https://github.com/boostorg/beast/pull/551  

* Serialize in one step when possible  
* Add basic_parser header and body limits  
* Add parser::on_header to set a callback  
  
API Changes:  
  
* Rename to message::base  
* basic_parser default limits are now 1MB/8MB  
  
Actions Required:  
  
* Change calls to message::header_part() with message::base()  
  
* Call body_limit and/or header_limit as needed to adjust the  
  limits to suitable values if the defaults are insufficient.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v70

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-28 05:13:12 UTC  
> Updated_at: 2017-06-28 20:18:23 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311558326  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=h1) Report  
> Merging [#551](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/3e1061b6d49c114a6f05938986b9a79f82275382?src=pr&el=desc) will **increase** coverage by `0.1%`.  
> The diff coverage is `95.71%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/551/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           master     #551     +/-   ##  
=========================================  
+ Coverage   93.73%   93.84%   +0.1%       
=========================================  
  Files          94       94               
  Lines        7134     7388    +254       
=========================================  
+ Hits         6687     6933    +246       
- Misses        447      455      +8  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.71% <100%> (+0.22%)` | :arrow_up: |  
| [include/beast/http/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.43% <100%> (+0.13%)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `80.48% <75%> (-1.57%)` | :arrow_down: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.11% <96.36%> (-0.52%)` | :arrow_down: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `90.59% <0%> (-1.3%)` | :arrow_down: |  
| [include/beast/websocket/detail/pausation.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BhdXNhdGlvbi5ocHA=) | `76.19% <0%> (-1.09%)` | :arrow_down: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `94.21% <0%> (-0.55%)` | :arrow_down: |  
| ... and [56 more](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=footer). Last update [3e1061b...5b68615](https://codecov.io/gh/vinniefalco/Beast/pull/551?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-28 20:42:23 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311783437  

Apparently I can't get pull requests right this week (`_attribute__` anyone?) So if you would like to sign off on this @octopus-prime I would appreciate it. I've invited you to become a Collaborator on the repository.

---

## Comment 3

> Username: octopus-prime  
> Created_at: 2017-06-28 20:51:34 UTC  
> Updated_at: 2017-06-28 20:52:29 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311786325  

> I've invited you to become a Collaborator on the repository.  
  
How do i accept this?  
Sign off + sign in ?!

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-06-28 20:52:30 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311786633  

>How do i accept this?  
  
I think its either a GitHub notification or email

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2017-06-28 20:53:41 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311787028  

Found it. Accepted it. Thanks!

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-06-28 20:56:18 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311787871  

> So if you would like to sign off on this @octopus-prime I would appreciate it.  
  
What do you mean? What do i have to do?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-06-28 20:56:57 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311788110  

>What do you mean? What do i have to do?  
  
I added you as a reviewer so there's a "Start Review" button and if you think this branch is okay then just press "Accept" or whatever its called, if you look around on the page you'll find it.

---

## Comment 8

> Username: octopus-prime  
> Created_at: 2017-06-28 21:06:06 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311790794  

I am unsure...   
  
![bildschirmfoto vom 2017-06-28 23-02-29](https://user-images.githubusercontent.com/7620013/27660100-2ee90fc4-5c56-11e7-8b86-b6759fa3fe46.png)  
![bildschirmfoto vom 2017-06-28 23-02-54](https://user-images.githubusercontent.com/7620013/27660105-33ebaaae-5c56-11e7-9e6a-96b71b1d40dc.png)  
  
Simply "Rebase and merge" or "Review"+"Approve"?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-06-28 21:22:34 UTC  
> Updated_at: 2017-06-28 21:22:57 UTC  
> Url: https://github.com/boostorg/beast/pull/551#issuecomment-311796015  

You can just do the reviews, I'll take care of merging the branches. Never use the GitHub interface to merge the branch (well don't merge at all, I will do it) - sometimes Github creates a merge commit and I try very hard to keep the history linear. Sometimes merge commits are unavoidable but when they happen they are strategically planned.  
  
When a branch is merged I have to update the docs as well so leave it to me - thanks!!!

---
