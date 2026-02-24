# #16 Fixed bug 10231 partly: If finish_edge was called, then now correctly. [Merged]

> Username: gatlex  
> Created at: 2014-08-01 12:22:08 UTC  
> Updated at: 2019-02-25 00:37:26 UTC  
> Merged at: 2016-05-01 20:00:19 UTC  
> Closed at: 2016-05-01 20:00:19 UTC  
> Url: https://github.com/boostorg/graph/pull/16  

Written in conjunctive because the bug that it never gets called with the current construction remains.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-10-31 20:57:49 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-61330477  

I'm not sure I understand why this patch is necessary.  I wrote a small test case and it seems to call finish_edge correctly for every edge.  Can you explain why this is necessary (perhaps a test case)?

---

## Comment 2

> Username: gatlex  
> Created_at: 2014-11-04 08:54:06 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-61609917  

Did you try the example graph in the MWE in the [bug ticket](https://svn.boost.org/trac/boost/ticket/10231)?  
  
I had two problems with finish_edge. The first is that it never gets called (which is an issue of the implementation that makes its presence optional).  
  
When I hard-code the call it gets called for each edge, but the calls happen at the wrong time.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2014-11-09 00:07:40 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62284373  

I just pushed a fix to ensure that finish_edge is actually called when defined.  Could you test if finish_edge is now called correctly?

---

## Comment 4

> Username: gatlex  
> Created_at: 2014-11-10 12:37:08 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62378538  

With that commit, I'm not able to compile the MWE (using gcc 4.8.2). It complains at the line _BOOST_TTI_HAS_MEMBER_FUNCTION(finish_edge)_ with the following error message:  
_.../boost/boost/graph/depth_first_search.hpp:63:5: note: standard conversions are not allowed in this context_ after a lot of instantiation hints.

---

## Comment 5

> Username: Belcourt  
> Created_at: 2014-11-10 20:45:44 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62451882  

Since dfs finish_edge is optional, we must use some introspection mechanism (Boost TTI) to determine if the user has supplied a finish_edge method in their visitor.  Unfortunately, as documented in TTI and confirmed by testing, only clang, Intel and gcc 4.9 and later can correctly detect this.  This means that older gcc, and many other compilers will not support finish_edge.  Strangely, it seems that older MSVC can detect finish_edge though newer versions can not.  
  
Anyway, I figure it's better to enable detection on working compilers than to leave it disabled on all compilers.  I will push a commit to protect the TTI code on working compilers and revert to the old syntax for non-working compilers.

---

## Comment 6

> Username: Belcourt  
> Created_at: 2014-11-11 05:06:36 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62502732  

I just pushed changes to condition use of TTI on the compilers that support working has_member_function for member templates.  This includes gcc 4.9 and newer, Intel 12 and newer, and all clang versions.  Basically finish_edge won't get called unless you're on one of these compilers.  I think that's the best we can do right now and you can probably close this ticket unless you have other ideas.

---

## Comment 7

> Username: gatlex  
> Created_at: 2014-11-11 10:30:38 UTC  
> Updated_at: 2014-11-11 11:00:36 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62528817  

Well, I may be wrong, but your preprocessor-directive does not work for gcc versions 5.x or higher, does it? I'd suggest something along the lines of  
    #if (defined(**GNUC**) && (**GNUC** > 4) || ((**GNUC** >= 4) && (**GNUC_MINOR** >= 9))) || \  
  
What about a compiler-flag that enables calling finish_edge checking for presence using TTI?  
  
Apart from that I have to little experience in those compiler-hacking-things to assess whether it is possible to work around it for older compilers. But I thought having read something about SFINAE to detect the presence of a member function on stackoverflow. And it was a while ago that I read it so I wonder whether it isn't possible with older gcc versions.  
  
If closing this ticket, I think it fair to point out the issue to users in the [usage documentation](http://www.boost.org/doc/libs/1_57_0/libs/graph/doc/depth_first_search.html).

---

## Comment 8

> Username: gatlex  
> Created_at: 2014-11-11 10:35:56 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62529442  

Most importantly: There are actually 2 issues with finish_edge, the second being that it is not called at the correct control points of the algorithm. This is what this pull request was actually all about and my patch doesn't seem to be included in your commits. (I tested it with a variant that calls finish_edge hard-coded.) Could you please include the patch in a commit.

---

## Comment 9

> Username: Belcourt  
> Created_at: 2014-11-11 19:01:24 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-62598067  

Yes, there's several other macro changes needed to finish this patch.  I'm making changes piecemeal so I don't accidentally break a working platform (or trying to anyway).  You're correct that I need to fix the logic for gcc 5, thanks for pointing that out.  I also need to condition for c++0x builds as some of them don't work correctly either.  
  
You're issue is two fold, finish_edge called in the wrong place, and finish_edge not being called at all.  I've been working on getting finish_edge called.  Once I'm done with that, we'll go back to figuring out the correct place to call it from, as you seem to have already done.

---

## Comment 10

> Username: gopchandani  
> Created_at: 2019-02-25 00:37:25 UTC  
> Url: https://github.com/boostorg/graph/pull/16#issuecomment-466835604  

I am observing this behavior in boost version 1.67... I tried the test file, same thing, the finish_edge in the visitor does not get called, whereas I can see others being called...  
  
My g++/gcc version is 5.4.0.

---
