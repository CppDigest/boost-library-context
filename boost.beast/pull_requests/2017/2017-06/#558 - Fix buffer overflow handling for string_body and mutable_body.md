# #558 Fix buffer overflow handling for string_body and mutable_body [Closed]

> Username: octopus-prime  
> Created at: 2017-06-29 18:27:41 UTC  
> Updated at: 2017-07-03 21:51:56 UTC  
> Closed at: 2017-07-03 21:51:56 UTC  
> Url: https://github.com/boostorg/beast/pull/558  

issue #553

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-06-29 18:28:28 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312054851  

Is there an easy way to add a test for this? Maybe with a custom string allocator that has a small max?

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-06-29 18:33:52 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312057335  

custom string allocator ... i never tried before...  
will have a look to find infos about...  
  
please note:  
  
this bugfix is the fix only. it does not contain optimizations you suggested yesterday.  
  
and, if i add tests:  
  
do i have to do git magic to reduce the number of commits again?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-06-29 18:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312057668  

>do i have to do git magic to reduce the number of commits again?  
  
Yes. But I can squash it for you if you want.

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-06-29 18:40:56 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312061441  

> But I can squash it for you if you want.  
  
fine. would not like to make the fix for a 3rd time :D (i have to work hard on my git skills, i know)  
  
the tests would only be possible for `mutable_body`, since `string_body` is tight bounded to `std::string`  
so no chance to use a custom allocator with `string_body`

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-06-29 18:48:15 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312064995  

No point to `mutable_body` test, since files in `example/` are excluded from coverage.

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-06-29 18:49:07 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312065228  

So not adding tests?!

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-06-29 18:49:44 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312065388  

There's no way to test it in a meaningful way until we can control the allocator. See: https://github.com/vinniefalco/Beast/issues/559

---

## Comment 8

> Username: octopus-prime  
> Created_at: 2017-06-29 18:53:02 UTC  
> Updated_at: 2017-06-29 18:53:25 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312066270  

Roger that. So you can merge after build is okay.  
  
> There's no way to test it in a meaningful way until we can control the allocator. See: #559  
  
That are the reasons why i suggested the `mutable_body`...

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-06-29 19:10:01 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312070514  

>That are the reasons why i suggested the mutable_body...  
  
Right, but `mutable_body` is a little bit too broad to be a public interface, that's why I think it belongs in example/. However, since we already have `string_body` its okay to broaden the scope a little bit to include `basic_string_body<CharT, Traits, Allocator>` for the public interfaces. This will allow us to test.

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2017-06-29 19:14:15 UTC  
> Updated_at: 2017-06-29 19:15:05 UTC  
> Url: https://github.com/boostorg/beast/pull/558#issuecomment-312071571  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=h1) Report  
> Merging [#558](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `57.14%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/558/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #558      +/-   ##  
===========================================  
- Coverage    93.77%   93.75%   -0.03%       
===========================================  
  Files           94       94                
  Lines         7393     7394       +1       
===========================================  
- Hits          6933     6932       -1       
- Misses         460      462       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.18% <57.14%> (-7.13%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=footer). Last update [78a065b...071cec4](https://codecov.io/gh/vinniefalco/Beast/pull/558?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
