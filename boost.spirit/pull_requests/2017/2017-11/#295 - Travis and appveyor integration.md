# #295 Travis and appveyor integration [Closed]

> Username: octopus-prime  
> Created at: 2017-11-26 16:05:35 UTC  
> Updated at: 2017-11-28 14:23:01 UTC  
> Closed at: 2017-11-28 14:23:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/295  

Is for #294 "travis and appveyor integration" and for #251 "Add continuous integration builds to improve code and pull request quality".  
  
- Added a .travis.yml file to configure building/running the tests.  
- Added a appveyor.yml file to configure building/running the tests.  
- Added a table to README.md to show build results.  
- Removed useless dependency to boost-system.  
- Fixed bug found by appveyor (#298).

---

## Comment 1

> Username: octopus-prime  
> Created_at: 2017-11-26 16:10:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#issuecomment-347019355  

Ouch... There is #291

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2017-11-27 15:42:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/295#pullrequestreview-79207840  

You may want to add:  
  
This fixes #251   
This fixes #294  
  
To the description so that when merged it will close out issues in the backlog that exist for it.

📁 .travis.yml

```diff
  43 |+   - export PATH="`pwd`":$PATH
  44 |+ 
  45 |+   - git submodule update --init tools/build
```

> Username: jeking3  
> Created_at: 2017-11-27 15:39:20 UTC  
> Updated_at: 2017-11-27 21:18:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153231828  

It would be easier to use the boostdep tool here so that future changes don;t cause this list to need to be updated manually.  
  
See the install phase of the [Boost.Uuid .travis.yml ](https://github.com/boostorg/uuid/blob/develop/.travis.yml)for an example.

> Username: octopus-prime  
> Created_at: 2017-11-27 16:06:36 UTC  
> Updated_at: 2017-11-27 21:18:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153240878  

nice hint, thanks! I will have a look...  
on the other side: now we see the HUGE dependencies to other libs - this might be nice to know.

---

📁 .travis.yml

```diff
   9 |+       - llvm-toolchain-trusty-5.0
  10 |+ 
  11 |+ matrix:
```

> Username: jeking3  
> Created_at: 2017-11-27 15:39:41 UTC  
> Updated_at: 2017-11-27 21:18:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153231951  

How come only C++14 is being tested?

> Username: octopus-prime  
> Created_at: 2017-11-27 16:04:24 UTC  
> Updated_at: 2017-11-27 21:18:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153240243  

i think c++14 is default theses days. and because of x3.  
but now i think x3 is not tested, because it's not part of test/Jamfile.  
we can add much more entries to the matrix...

> Username: jeking3  
> Created_at: 2017-11-27 21:35:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153330354  

I work on projects ranging from C++03 to C++14.  We still have requests to support Visual Studio 2008.  I wouldn't assume C++14 is the default for anybody.

> Username: octopus-prime  
> Created_at: 2017-11-27 21:40:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153331673  

I think we need to add a lot more compiler versions and c++ dialects  
- after build system integration works fine  
- after separating tests into smaller parts to run more matrix builds (hopefully parallel)  
  
For now I am very happy to see ANY results...


📁 README.md

```diff
  11 |+ Branch             | Linux/OSX        | Windows           |Coverage          
  12 |+ -------------------|------------------|-------------------|------------------
  13 |+ travis_integration | [![Build Status](https://travis-ci.org/octopus-prime/spirit.svg?branch=travis_integration)](https://travis-ci.org/octopus-prime/spirit.svg?branch=travis_integration) | |
```

> Username: jeking3  
> Created_at: 2017-11-27 15:40:15 UTC  
> Updated_at: 2017-11-27 21:18:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153232122  

Shouldn't this use the boostorg account instead of octopus-prime?

> Username: octopus-prime  
> Created_at: 2017-11-27 16:07:39 UTC  
> Updated_at: 2017-11-27 21:18:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#discussion_r153241173  

yes of course! after merging and travis activation for spirit there should be entered the boostorg links!  
  
https://travis-ci.org/boostorg/spirit.svg?branch=master  
https://travis-ci.org/boostorg/spirit.svg?branch=develop


---

## Comment 3

> Username: octopus-prime  
> Created_at: 2017-11-27 16:09:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#issuecomment-347231521  

@jeking3 Thanks for review. But there is #291. So it's likely this pull request will be closed without merge...

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-11-28 13:22:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#issuecomment-347521755  

Please do one thing per PR.

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2017-11-28 13:37:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#issuecomment-347525431  

> Please do one thing per PR.  
  
You are right!  
  
Perhaps you can cherry pick and close this PR.

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-11-28 14:23:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/295#issuecomment-347538241  

Useless

---
