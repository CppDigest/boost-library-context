# #61 Fix #11374 and #12038: issues with `find_flow_cost()`, bundled properties and named parameters [Merged]

> Username: maelvls  
> Created at: 2016-03-02 17:23:59 UTC  
> Updated at: 2020-10-31 16:45:26 UTC  
> Merged at: 2016-05-02 17:11:23 UTC  
> Closed at: 2016-05-02 17:11:23 UTC  
> Url: https://github.com/boostorg/graph/pull/61  

I talked about that problem on [stackoverflow](http://stackoverflow.com/questions/30621255/using-named-parameters-and-bundled-properties-with-edmonds-karp-max-flow). This PR solves two different tickets because the cause was almost the same (bundled properties had not been tested thoroughly).    
Here are the tickets:   
- Issue [#11374](https://svn.boost.org/trac/boost/ticket/11374): `find_flow_cost()` won't work with bundled properties in conjonction to named parameters  
- Issue [#12038](https://svn.boost.org/trac/boost/ticket/12038): every max-flow function (e.g. `edmonds_karp_max_flow()`) won't work with named parameters   
  
I apologize for having mixed those two issues in one PR ; I did try to separate them, but the last one (bundled properties and named parameters with `find_flow_cost()`) needed the two other commits.

---

## Comment 1

> Username: maelvls  
> Created_at: 2016-03-02 17:27:03 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-191338187  

By the way, do we need to write unit tests on each of these changes? I am a bit lost when it comes to UT on Boost; there doesn't seem to be any continuous integration or any script that builds and run all tests.

---

## Comment 2

> Username: Belcourt  
> Created_at: 2016-04-24 04:44:57 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-213888473  

Yes, there's ways to run all the tests.  Checkout boost, initialize the submodules, b2 headers, cd status, ../b2 -j8  or similar.  To run just graph tests, edit status/Jamfile.v2 and remove all the libraries except graph/test.  
  
I would like to update the tests as all graph tests pass at present with these issues.  Can you add a bundled property test for both max-flow and find-flow-cost that fails with develop, and passes with your patch?

---

## Comment 3

> Username: maelvls  
> Created_at: 2016-04-24 15:48:12 UTC  
> Updated_at: 2020-10-31 16:45:26 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-213987496  

Thanks for reading my PR and for your help!  
  
Ok, I'll try to write a test that would "reveal" the issue when tested on `develop`. The only slight issue I have is to find where I should put those tests in:   
- in existing `graph/test/max_flow_test.cpp` (= mess with existing tests)  
- in new files, e.g.  
  - `test_max_flow_with_bundled_params.cpp`  
  - `test_find_flow_cost_with_named_and_bundled_params.cpp`  
  
I'm a bit lost :/

---

## Comment 4

> Username: Belcourt  
> Created_at: 2016-04-24 16:20:00 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-213996058  

I think new test files would be fine.  Let me know if I can help.

---

## Comment 5

> Username: maelvls  
> Created_at: 2016-04-25 11:59:46 UTC  
> Updated_at: 2016-04-25 15:40:05 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-214282594  

Here is the test for issue [#11374](https://svn.boost.org/trac/boost/ticket/11374) (`find_flow_cost` with bundled properties and named parameters).  
  
`./b2 status` to launch the test.   
  
At first I made a compile-time test, but then I made this test "runnable" (with real graph and everything) to make sure bundled properties would be handled the same way as internal properties would.  
  
As I rely on another code to generate the graph, so I had to modify it and make it more generic.

---

## Comment 6

> Username: maelvls  
> Created_at: 2016-04-25 12:58:46 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-214304355  

I'm wondering: should I forget about testing the results (flow_cost=29) and only focus on revealing the compile-time flaw of `find_flow_cost`?   
  
Because I'm kind of re-doing the tests of `successive_shortest_path_nonnegative_weights_test.cpp`...

---

## Comment 7

> Username: maelvls  
> Created_at: 2016-04-25 16:13:44 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-214423034  

I'm done with the two unit tests! I tested them before/after the modifications to be sure.  
  
What do you think?

---

## Comment 8

> Username: Belcourt  
> Created_at: 2016-04-25 22:07:18 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-214545103  

This looks really good, thanks.  I'm testing them now and will push if clean.

---

## Comment 9

> Username: maelvls  
> Created_at: 2016-04-26 10:00:13 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-214689869  

Btw, I thought it would be a good idea to have a continuous integration service as travis-ci or circle to run the tests automatically and see the results on the PRs. Here is my PR for that: #63

---

## Comment 10

> Username: Belcourt  
> Created_at: 2016-05-01 19:35:10 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-216066855  

So my only complaint with this patch is you use tabs instead of spaces.  Can you please indent with two spaces for each nested scope and don't use tabs?  Otherwise this is fine.

---

## Comment 11

> Username: maelvls  
> Created_at: 2016-05-02 10:11:32 UTC  
> Updated_at: 2016-05-02 10:56:57 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-216186939  

Sure, sorry for that! So no tabs and instead 2 spaces per indentation level (I hope I understood correctly!)  
  
Didn't read thoroughly the [coding policy](http://www.boost.org/community/policy.html) which specifically says not to use tabs 😕   
  
Btw, isn't the rest of the code being indented with 4 spaces per indentation level?

---

## Comment 12

> Username: maelvls  
> Created_at: 2016-05-02 10:17:09 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-216188908  

Oh, a conflict with `min_cost_max_flow_utils.hpp`... I'll take a look

---

## Comment 13

> Username: maelvls  
> Created_at: 2016-05-02 10:36:56 UTC  
> Updated_at: 2016-05-02 10:57:29 UTC  
> Url: https://github.com/boostorg/graph/pull/61#issuecomment-216194041  

I (carefully) fixed the conflict and rebased.   
  
I could, as you wish, also "squash" the last fixup commit.

---
