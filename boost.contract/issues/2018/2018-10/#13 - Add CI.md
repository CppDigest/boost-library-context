# #13 - Add CI [Closed]

> Username: jeking3  
> Created at: 2018-10-27 21:41:15 UTC  
> Updated at: 2019-06-03 20:45:33 UTC  
> Closed at: 2019-06-02 16:39:59 UTC  
> Url: https://github.com/boostorg/contract/issues/13  

Consider adding https://github.com/jeking3/boost-ci to improve quality.

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-10-30 17:45:35 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-434402112  

I am not familiar with Boost.CI (is it an officially release component of Boost, like BJam?). I'll look into it can consider it if/when a new release of Boost.Contract is required.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-30 21:47:55 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-434482269  

Not officially, but I am part of the Community Maintenance Team and have been using it over the last year on about 20 boost repositories to improve quality.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-10-30 21:49:01 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-434482583  

If you copy the LICENSE file out of Boost.Assign into the top level of your repo, GitHub will automatically tag it using BSL-1.0 for licensing.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-04 22:51:44 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-435715986  

The AppVeyor build results should be ready around 12AM EST.  
Travis is building now.  I opened some issues related to the failures I've seen so far.  
  
AppVeyor Build Results: https://ci.appveyor.com/project/jeking3/contract/builds/20042520  
Travis CI Build Results: https://travis-ci.org/jeking3/contract/builds/450610128

---

## Comment 5

> Username: lcaminiti  
> Created at: 2018-12-22 14:00:49 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-449572433  

OK, I will consider adding Boost.CI to Boost.Contract.

---

## Comment 6

> Username: lcaminiti  
> Created at: 2019-06-02 16:39:59 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-498046535  

I won't have time to add Boost.CI to Boost.Contract in release 1.1.0. However, this would be a good thing to do since it already found a couple of errors when it was ran locally by jeking3... it could also be done by whomever has the time to do so. Maybe do it and send me a pull request for consideration.

---

## Comment 7

> Username: jeking3  
> Created at: 2019-06-03 20:19:36 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-498410663  

I'm working on this:  
  
https://github.com/jeking3/contract/pull/1  
  
However the test suite for Boost.Contract seems to take a long time and is causing timeouts.  I have to pare down the language levels supported in the build jobs I guess.  Why do the tests take such a long time?  I haven't looked yet.

---

## Comment 8

> Username: lcaminiti  
> Created at: 2019-06-03 20:40:08 UTC  
> Updated at: 2019-06-03 20:45:33 UTC  
> Url: https://github.com/boostorg/contract/issues/13#issuecomment-498418050  

Make sure you only run the tests under the test/ directory (you don't need to run the examples under the example/ directory instead). I think the tests take a long time just because there's really a lot of them, one for every feature and there's quite a few combinations to test. Take a look at the size of this test matrix: https://www.boost.org/development/tests/develop/developer/contract.html  
  
NOTE: There's actually ever more combinations if you consider contracts that can be selectively disabled at compile-time #defining the BOOST_CONTRACT_NO_... macros but these extra test permutations, which really take days to run, are not part of the default test build unless you specify extra bjam parameters bc_no=... so I am sure you are not running those.
