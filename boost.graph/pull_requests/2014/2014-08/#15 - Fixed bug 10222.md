# #15 Fixed bug 10222. [Merged]

> Username: gatlex  
> Created at: 2014-08-01 12:20:16 UTC  
> Updated at: 2014-11-12 08:26:24 UTC  
> Merged at: 2014-11-12 03:57:50 UTC  
> Closed at: 2014-11-12 03:57:50 UTC  
> Url: https://github.com/boostorg/graph/pull/15  

Formerly, root_map stored a reference to the "parent" in the spanning tree of the connected component. While this is also interesting (and could be used to compute the root), this is not what the docu claims is computed. (I'm working on a variant that records the forward/backward paths within a component.)

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-11-02 21:35:19 UTC  
> Url: https://github.com/boostorg/graph/pull/15#issuecomment-61424762  

I just updated ticket #10222 as I believe the code is working correctly as documented.  If you have access to The BGL by Siek et. al, could you look at the strong components algorithmic description bottom of page 198 as that's where the computation of the root is described.

---

## Comment 2

> Username: gatlex  
> Created_at: 2014-11-04 08:35:08 UTC  
> Url: https://github.com/boostorg/graph/pull/15#issuecomment-61608122  

At least the [documentation](http://www.boost.org/doc/libs/1_57_0/libs/graph/doc/strong_components.html) (and, btw, also the [BGL book](http://read.pudn.com/downloads128/ebook/549039/boost%20graph%20library.pdf)) says "there is a single root vertex for each component". As I interstand that phrase, two vertices x and y should be in the same SCC iff root[x] == root[y]. I refered to the [BGL book](http://read.pudn.com/downloads128/ebook/549039/boost%20graph%20library.pdf), where it is written "The root field will end up mapping each vertex to the topmost vertex in the same strongly connected component." This I understand in the very same way.  
  
Both statements, however, do not hold true with the current implementation. For details refer to the [bug ticket](https://svn.boost.org/trac/boost/ticket/10222), where an example of a one-SCC-graph with non-constant root-map is given.  
  
As an aside, I have to admit that I didn't check with Boost 1.57 due to lack of time, but I think the problem persists.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2014-11-08 20:13:20 UTC  
> Url: https://github.com/boostorg/graph/pull/15#issuecomment-62276920  

After digging into this a bit more, I believe you're correct.  The test case in your bug ticket should return 0 for all three vertices.  Since this is a bug, I'd like a test case to push along with this patch.  Can you create a small test case and add it to this PR (in the test rather than the examples directory)?

---

## Comment 4

> Username: gatlex  
> Created_at: 2014-11-11 10:11:35 UTC  
> Url: https://github.com/boostorg/graph/pull/15#issuecomment-62526762  

I added a test case _test/strong_components_test.cpp_ and updated _test/regression.cfg_.   
I'm quite new to Boost, so please double check whether the test is correctly integrated into the test suite (i.e., correctly reports the status and gets correctly called by the build scripts).

---

## Comment 5

> Username: Belcourt  
> Created_at: 2014-11-12 02:50:54 UTC  
> Url: https://github.com/boostorg/graph/pull/15#issuecomment-62661642  

Sorry, I wasn't clear Alex.  You have to add the new test to the test/Jamfile.v2 (emulate one of the run targets already in that file).  The test/regression.cfg file is dead, hasn't been updated since Jeremy added in 2001.  I'll remove this file as I'm confident it's unused.

---

## Comment 6

> Username: gatlex  
> Created_at: 2014-11-12 08:26:24 UTC  
> Url: https://github.com/boostorg/graph/pull/15#issuecomment-62684680  

Thanks for updating the Jamfile :)

---
