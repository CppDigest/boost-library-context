# #114 First batch of merges from the backlog. [Merged]

> Username: anadon  
> Created at: 2018-09-01 04:00:55 UTC  
> Updated at: 2018-10-12 22:28:26 UTC  
> Merged at: 2018-10-12 17:32:24 UTC  
> Closed at: 2018-10-12 17:32:24 UTC  
> Url: https://github.com/boostorg/graph/pull/114  

Added PR's #80, #93, #95, #111  These are well formed bugfix PR's which should be added.  All tests are passing locally, but I don't have the facilities to test more broadly.  The broader test facilities would be a future feature addition and out of scope of what I've been immediately tasked with.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2018-09-01 06:13:56 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417836349  

@anadon I just want to say thank you so much for getting things moving! I have recently been trying to help some contributors get their PR into good shape to smooth the acceptance process, but I didn't expect that someone with merge permission would swoop in so soon to actually clear up the backlog. Cheers.

---

## Comment 2

> Username: anadon  
> Created_at: 2018-09-01 06:15:12 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417836395  

I don't have write permissions.  I'm just making things easier.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2018-09-01 06:21:12 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417836616  

Oh, OK. Why are you merging multiple PRs into batches? If they get merged as a batch and we realize afterwards that something broke, won't it be harder to identify and revert the specific original PR that caused the break?

---

## Comment 4

> Username: anadon  
> Created_at: 2018-09-01 06:37:00 UTC  
> Updated_at: 2018-09-01 06:37:51 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417837233  

I was directed to start catching up on the backlog and this was more expedient.  On balance, it is better to get the obvious and easy stuff in quick since there are PR's waiting since 2014 and then suss through the ones that aren't trivial.  
  
If I end up the active maintainer for graph, or someone else dedicates time, then pull requests could be handled as they come which is more proper.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-09-01 07:12:05 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417838891  

>I was directed to start catching up on the backlog and this was more expedient.  
  
Actually I meant you to batch the trivial one AFTER CI was set up, otherwise it's just a bunch of changes mashed together ;)  Probably I didn't make that clear - sorry about that!  
  
Anyhow I've enabled travis and appveyor, time permitting I'll sort out some CI scripts later...

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-09-01 12:11:12 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417855492  

OK CI is now enabled, can you "touch" one of the files in this PR?  Hopefully that will trigger a fresh CI build.  
  
There is currently one CI failure in develop: https://travis-ci.org/boostorg/graph/jobs/423317402   
  
relaxed_heap_test fails for gcc-8.1 in C++03 mode, I can reproduce here and gdb reports:  
```  
  
0%   10   20   30   40   50   60   70   80   90   100%  
|----|----|----|----|----|----|----|----|----|----|  
*Assertion failed!  
  
Program: D:\data\boost\boost\bin.v2\libs\graph\test\relaxed_heap_test.test\gcc-8.1.0s03\debug\relaxed_heap_test.exe  
File: ..\..\../boost/optional/optional.hpp, Line 1205  
  
Expression: this->is_initialized()  
```  
  
I've no idea if this is a graph or Boost.Optional issue.

---

## Comment 7

> Username: anadon  
> Created_at: 2018-09-01 14:17:56 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417862718  

Let me sort through this later today.  I'm just waking up after a late night.

---

## Comment 8

> Username: anadon  
> Created_at: 2018-09-01 14:48:41 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417864585  

Looks like relaxed head is being used as a priority heap, mainly coded in 2004.  I think replacing it with boost.heap would be a good idea.  Thoughts @jzmaddock ?  
  
It also looks like there are a number of 'pending' files which need attention from way back when.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-09-01 15:54:54 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417868700  

Actually, I'm looking at the stdlib implementation of priority queues and heaps and they are old enough that they look like they might be drop in replacements.  I'll look into how viable those are.  There is a lot of complexity in graph that I'm hoping is not necessary.

---

## Comment 10

> Username: anadon  
> Created_at: 2018-09-01 16:03:24 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417869223  

It actually looks like the relaxed_heap is not used elsewhere, users don't have a method to enable it, a google search yields no used examples for a total of 6 results, and related structures in code are just not used.  With it being vestigial, I'd like to remove it.  I'd like a go-ahead before I muck around with that.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2018-09-01 17:27:29 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417874824  

>It actually looks like the relaxed_heap is not used elsewhere, users don't have a method to enable it, a google search yields no used examples for a total of 6 results, and related structures in code are just not used. With it being vestigial, I'd like to remove it. I'd like a go-ahead before I muck around with that.  
  
A quick bit of grepping around: There are a few headers which include it, but don't use it, those #includes should be removed.  
  
It is still used in dijkstra_shortest_Paths.hpp if BOOST_GRAPH_DIJKSTRA_USE_RELAXED_HEAP is defined, but this is not the default, and it appears not to be documented anywhere.  
  
So... my suggestion would be to remove all references to it, but leave the header in place, and add some #pragmas to it to loudly report it as deprecated and unsupported.

---

## Comment 12

> Username: anadon  
> Created_at: 2018-09-01 22:06:48 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417890414  

There are things in iteration_macros.hpp that I never could have imagined are valid syntax.  They certainly are not in best practice.  Attending to those now.  I don't know why my system is not picking these up.

---

## Comment 13

> Username: swatanabe  
> Created_at: 2018-09-01 23:37:44 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417894643  

#80 looks suspect to me.  It claims to be a  
straight up bug fix, but the actual code change  
is more than that.  The original code forbids adding  
a vertex that is already present in the subgraph  
and this appears to be intentional, because of the  
assert.  Changing the preconditions like this, at  
a minimum, requires clarification in the documentation  
about exactly what the preconditions are.

---

## Comment 14

> Username: anadon  
> Created_at: 2018-09-01 23:45:13 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417894907  

@swatanabe  Could you be more specific in the issue you are seeing?

---

## Comment 15

> Username: swatanabe  
> Created_at: 2018-09-02 00:14:31 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417896037  

AMDG  
  
On 09/01/2018 05:45 PM, jrmarsha wrote:  
> @swatanabe  Could you be more specific in the issue you are seeing?  
>   
  
  The PR claims that it fixes a bug wherein add_vertex  
fails to add the vertex to the ancestors of a subgraph,  
breaking the subgraph invariants.  That's all well  
and good, so far, and the new behavior is clearly  
correct according to the documentation.  The problem is  
that it /also/ permits inserting a vertex into the  
root and inserting a vertex which is already present,  
both of which were previously disallowed.  It is not  
clear from the documentation whether these should be  
allowed or not.  I am not claiming that these additional  
changes are necessarily wrong, but API changes like  
this need to be done intentionally, and not as an  
inadvertent side-effect of other changes.  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: anadon  
> Created_at: 2018-09-02 00:18:19 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-417896154  

I'll review it when I get the current builds to stop failing.  Thank you for bringing it to my attention.

---

## Comment 17

> Username: anadon  
> Created_at: 2018-09-07 13:17:59 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-419436240  

@jzmaddock Can I get you opinion on kicking up the compile requirement to C++11 on this?  I'm having too much trouble on the typename abuse, and all the remedies I know how to do depend on C++11.

---

## Comment 18

> Username: swatanabe  
> Created_at: 2018-09-07 14:53:56 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-419465128  

AMDG  
  
On 09/06/2018 10:04 PM, jrmarsha wrote:  
> @anadon pushed 1 commit.  
>  
> 3cc890e  Changing c++11 auto type usage to  
> delctype() boilerplate in order to allow compatability with C++03.  
  
decltype is /also/ C++11.  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: swatanabe  
> Created_at: 2018-09-07 14:59:12 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-419466834  

AMDG  
  
On 09/07/2018 07:18 AM, jrmarsha wrote:  
> @jzmaddock Can I get you opinion on kicking up the compile requirement to C++11 on this?  I'm having too much trouble on the typename abuse, and all the remedies I know how to do depend on C++11.  
>   
  
  I have no idea what you mean by typename abuse.  Using  
typename for dependent nested types is mandatory  
inside templates.  It's not an abuse.  It's why  
the typename keyword exists in the first place.  
(Note: C++17 relaxes the requirements for typename,  
but only some of the time).  
  
In Christ,  
Steven Watanabe

---

## Comment 20

> Username: anadon  
> Created_at: 2018-09-07 19:08:49 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-419536974  

I caught that after I sent that.  I'm going to tally it up to learning  
mistakes.  
  
On Fri, Sep 7, 2018, 10:59 swatanabe <notifications@github.com> wrote:  
  
> AMDG  
>  
> On 09/07/2018 07:18 AM, jrmarsha wrote:  
> > @jzmaddock Can I get you opinion on kicking up the compile requirement  
> to C++11 on this? I'm having too much trouble on the typename abuse, and  
> all the remedies I know how to do depend on C++11.  
> >  
>  
> I have no idea what you mean by typename abuse. Using  
> typename for dependent nested types is mandatory  
> inside templates. It's not an abuse. It's why  
> the typename keyword exists in the first place.  
> (Note: C++17 relaxes the requirements for typename,  
> but only some of the time).  
>  
> In Christ,  
> Steven Watanabe  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/114#issuecomment-419466834>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC-CBYSXKbGT9xZ7gAMfWwt6IpJX07Heks5uYonBgaJpZM4WWD51>  
> .  
>

---

## Comment 21

> Username: swatanabe  
> Created_at: 2018-09-11 21:27:45 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-420431562  

AMDG  
  
The test cases are not templates.  typename is illegal  
outside of templates in C++03.  
  
Also, some of your lines are a bit overlong.  The  
existing code wraps similar lines.  Please try  
to match it.  
  
In Christ,  
Steven Watanabe

---

## Comment 22

> Username: anadon  
> Created_at: 2018-09-23 05:26:20 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-423792948  

@swatanabe It is looking like things are more stable.  With regards to your concern on PR #80, I think the changes make sense and don't break code and should be accepted.  With this passing more tests, I would like to have this set of patches added to increase general stability and so the next batch of patches can rebase on it.

---

## Comment 23

> Username: anadon  
> Created_at: 2018-09-23 16:06:22 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-423827143  

Can I get someone to restart the checks?  I know at least one more of them will pass if they are restarted.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2018-09-23 17:09:10 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-423831559  

I've restarted the GCC-8 test that failed with a network error, however the XCode failures look like new issues to me - graphviz test is failing on all the XCode tests and passing on Windows and Linux, something has definitely regressed here?

---

## Review 25 [Commented]

> Username: jzmaddock  
> Created_at: 2018-09-23 17:16:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/114#pullrequestreview-157945625  

📁 include/boost/graph/iteration_macros.hpp

```diff
  19 | 
  20 |- /*
  21 |-   BGL_FORALL_VERTICES_T(v, g, graph_t)  // This is on line 9
```

> Username: jzmaddock  
> Created_at: 2018-09-23 17:16:35 UTC  
> Updated_at: 2018-09-29 23:46:01 UTC  
> Url: https://github.com/boostorg/graph/pull/114#discussion_r219705251  

Is there a reason for deleting this comment block when it's a change not part of any of the PR's?

> Username: anadon  
> Created_at: 2018-09-23 17:19:30 UTC  
> Updated_at: 2018-09-29 23:46:01 UTC  
> Url: https://github.com/boostorg/graph/pull/114#discussion_r219705367  

It seemed like an old chunk of commented out code not used for anything, but did add clutter.  If you think that change should be reverted I'll do so.

> Username: jzmaddock  
> Created_at: 2018-09-23 17:24:00 UTC  
> Updated_at: 2018-09-29 23:46:01 UTC  
> Url: https://github.com/boostorg/graph/pull/114#discussion_r219705500  

Look more closely: it's explaining what the following macros do and how they work, so yes I think it should stay IMO.

> Username: anadon  
> Created_at: 2018-09-23 17:24:58 UTC  
> Updated_at: 2018-09-29 23:46:01 UTC  
> Url: https://github.com/boostorg/graph/pull/114#discussion_r219705527  

OK, I'll revert it today.

> Username: anadon  
> Created_at: 2018-09-25 14:22:50 UTC  
> Updated_at: 2018-09-29 23:46:01 UTC  
> Url: https://github.com/boostorg/graph/pull/114#discussion_r220213502  

Done.


---

## Comment 26

> Username: anadon  
> Created_at: 2018-09-23 17:22:19 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-423832471  

I'm not sure of why that regression is only hitting Mac, but it looks like it is related to undirected-directed graph conversion.  I don't have a system to test that on locally, so I may not be the best person to work on it.

---

## Comment 27

> Username: anadon  
> Created_at: 2018-09-23 19:39:29 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-423842170  

Looks like something funky is happening with boost::distance, so I'll be working on these a little bit longer.

---

## Comment 28

> Username: anadon  
> Created_at: 2018-09-25 14:22:31 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-424363653  

@jzmaddock Can I get you to change the TravisCI configuration to update all boost submodules?  I think that might be related to the gcc-8 failure, and if we're lucky the Mac failures.

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2018-09-25 17:11:51 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-424426242  

>Can I get you to change the TravisCI configuration to update all boost submodules? I think that might be related to the gcc-8 failure, and if we're lucky the Mac failures.  
  
It does run against the super-projects develop branch - this may be slightly (a few hours) behind develop for each sub-project, but is not the cause of the failures.  The gcc-8 failure was down to a network error pulling boost down the line - I restarted it and that one is now cleared.  The XCode failures look genuine to me, though I have no idea what's happening.

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2018-09-25 17:13:01 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-424426581  

Just on the off-chance, I've restarted one XCode job, just in case something has changed in develop.

---

## Comment 31

> Username: anadon  
> Created_at: 2018-09-25 17:26:23 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-424430832  

If you look at #118, I think that may actually be related to the cause (and thankfully the fix!) of the error.

---

## Comment 32

> Username: pdimov  
> Created_at: 2018-09-28 17:36:18 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425511185  

I don't understand why this PR groups several other PRs together. The original PRs should be merged one by one, as needed.

---

## Comment 33

> Username: anadon  
> Created_at: 2018-09-29 13:12:20 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425644294  

@pdimov Next time.

---

## Comment 34

> Username: pdimov  
> Created_at: 2018-09-29 13:19:28 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425644749  

Next time? What does this mean?

---

## Comment 35

> Username: anadon  
> Created_at: 2018-09-29 13:38:39 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425645985  

Granted, wouldn't be the worst idea to try and back up a bit. @jzmaddock, could I poke you to trigger travisCI runs on #80, #93, #95, and #111?

---

## Comment 36

> Username: pdimov  
> Created_at: 2018-09-29 13:42:39 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425646217  

Can't be triggered because they predate the addition of .travis.yml. They'll need to be rebased on the new `develop`, by their submitters, so that CI will run.  
  
But the first thing that needs to be done is to clear the failures. One guess is that the exception classes need to be marked with BOOST_SYMBOL_VISIBLE.

---

## Comment 37

> Username: anadon  
> Created_at: 2018-09-29 13:47:27 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425646489  

I noted that the PR that does that, #118, might be useful.  But there are other PRs without that change which pass on Mac.  I've been waiting to get access to a Mac to take more of a look at what is going on.

---

## Comment 38

> Username: pdimov  
> Created_at: 2018-09-29 13:58:04 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425647160  

Yes, #118 fixes these failures.

---

## Comment 39

> Username: anadon  
> Created_at: 2018-09-29 13:59:22 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425647235  

Well, I'm new here and you have much more history than I do.  I'll see about merging it in and see what happens.

---

## Comment 40

> Username: pdimov  
> Created_at: 2018-09-29 14:05:08 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425647655  

Your change removing the relaxed heap test contains far too many unrelated changes, mostly whitespace. Could you perhaps rework that into a PR that only adds the deprecation message to the header (inside the include guards, using `<boost/config/header_deprecated.hpp>`) and only comments out the line invoking the test (without deleting the test entirely)?

---

## Comment 41

> Username: anadon  
> Created_at: 2018-09-29 14:07:51 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425647863  

That test was causing active errors, was private, and largely unused.  I got an OK above about that particular thing.

---

## Comment 42

> Username: pdimov  
> Created_at: 2018-09-29 14:13:02 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425648304  

Yes I know. I was asking you for a PR so that I could merge it to clear the failure. But never mind, I'll do that myself.

---

## Comment 43

> Username: anadon  
> Created_at: 2018-09-29 17:19:46 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425661240  

@jzmaddock @swatanabe Looks like everything is going to pass.  Time for a (hopefully) final review and merge.

---

## Comment 44

> Username: pdimov  
> Created_at: 2018-09-29 23:10:59 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425681366  

I fixed the Xcode failures by merging #118 and the g++-8 failure by https://github.com/boostorg/graph/commit/a65f604d544616a2715abda913fa5e3d01738d18, so the Travis results are all green on develop at the moment.  
  
I'm now looking at the Range issue on g++ 4.6 and if I clear it, I'll reinstate the g++ 4.6 Travis configuration here as well.

---

## Comment 45

> Username: anadon  
> Created_at: 2018-09-29 23:16:48 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425681593  

@pdimov The way you're going about this is counter productive.  Please don't undermine this PR.

---

## Comment 46

> Username: pdimov  
> Created_at: 2018-09-30 01:54:43 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425687524  

No more _undermining_ will be forthcoming.  
  
John, I fixed the g++ 4.6 issue with Boost.Range, and the results here will show up at https://travis-ci.org/boostorg/graph/builds/435122722, when Travis gets around to it. The branch is `feature/gcc-4.6`, I leave the decision to merge (if the tests pass) to you.  
  
In addition, I've added an `-fsanitize=undefined` job on `feature/ubsan`, results should show up at https://travis-ci.org/boostorg/graph/builds/435123731.  
  
> So... my suggestion would be to remove all references to it, but leave the header in place, and add some #pragmas to it to loudly report it as deprecated and unsupported.  
  
Since `dijkstra_shortest_paths.hpp` unconditionally includes `relaxed_heap.hpp`, the loud reports show up on each direct or indirect use of it. The include should probably be either guarded by `#ifdef BOOST_GRAPH_DIJKSTRA_USE_RELAXED_HEAP`, or removed outright (along with the corresponding code block.)

---

## Comment 47

> Username: jzmaddock  
> Created_at: 2018-09-30 10:30:54 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425710894  

>John, I fixed the g++ 4.6 issue with Boost.Range, and the results here will show up at https://travis-ci.org/boostorg/graph/builds/435122722, when Travis gets around to it. The branch is feature/gcc-4.6, I leave the decision to merge (if the tests pass) to you.  
  
Merged, thanks.  
  
>In addition, I've added an -fsanitize=undefined job on feature/ubsan, results should show up at https://travis-ci.org/boostorg/graph/builds/435123731.  
  
Ugh, lots of failures - looks like all the same failure and that the library plays fast and loose with some typecasts?  
  
>Since dijkstra_shortest_paths.hpp unconditionally includes relaxed_heap.hpp, the loud reports show up on each direct or indirect use of it. The include should probably be either guarded by #ifdef BOOST_GRAPH_DIJKSTRA_USE_RELAXED_HEAP, or removed outright (along with the corresponding code block.)  
  
Good catch - I would tend to vote for a #if guard as the most conservative option, but don't have a strong view.  
  
>The way you're going about this is counter productive. Please don't undermine this PR.  
  
I'm sure that's not the intention at all.  I realise this is all getting way more complex than it first appeared, but unfortunately when a library isn't maintained for a while it can take a while to bash things into a shape where substantive patches can even be considered.  Once everyone is confident the testing regime is sound, then patches like this become much easier to evaluate.  
  
That just leaves the change in semantics in #80 highlighted by @swatanabe - let me see if I can produce a test case so we can all decide what the right thing to do is.

---

## Comment 48

> Username: jzmaddock  
> Created_at: 2018-09-30 12:03:43 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425716081  

OK, I'm going to write out what I think is happening here and you guys can all tell me I'm wrong :)  
  
* You cannot add a vertex to the root graph - the vertexes are already there when you create the graph, you can only join them together with edges.  Hence the original code had `BOOST_ASSERT(!g.is_root());`  
* The PR (correctly IMO) recursively adds vertexes to parent subgraphs so that we maintain the invariant that each child is a strict subset of it's parents.  
* The PR uses `is_root()` to determine when to abort from the recursion - the effect is to remove the assert and make adding vertexes to the root a no-op instead.  
* I'm in 2 minds whether the assertion is important to retain:  if we wished to reinstate it we could just change `add_vertex(u_global, g.parent());` to `if(!g.parent().is_root()) add_vertex(u_global, g.parent());`.  
* The only use case I could think of where the assertion might be important, is where an attempt is made to add a vertex with an invalid index - but I see this isn't checked for when adding vertexes to sub-graphs either, so perhaps it doesn't matter?

---

## Comment 49

> Username: pdimov  
> Created_at: 2018-09-30 12:54:44 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425719038  

> Ugh, lots of failures - looks like all the same failure and that the library plays fast and loose with some typecasts?  
  
False positives caused by the hidden visibility. I'll try to patch these up.  
  
I'm trying to convince the _visibility experts_ that visibility should propagate, so that we can just say `b2 visibility=global` on these runs and not have to specify it everywhere manually (https://github.com/boostorg/build/pull/333) - no luck so far.

---

## Comment 50

> Username: pdimov  
> Created_at: 2018-09-30 13:00:03 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425719421  

The integer overflow in floyd_warshall_test is not a false positive caused by hidden visibility though. :-)

---

## Comment 51

> Username: anadon  
> Created_at: 2018-09-30 13:23:13 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425720737  

Can some of these easier ones be left to me?  I'm trying to learn more  
about this.  
  
On Sun, Sep 30, 2018, 09:00 Peter Dimov <notifications@github.com> wrote:  
  
> The integer overflow in floyd_warshall_test is not a false positive caused  
> by hidden visibility though. :-)  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/114#issuecomment-425719421>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC-CBSvmKdmnvsgnm_U4dEYWCZKnQxxwks5ugMBVgaJpZM4WWD51>  
> .  
>

---

## Comment 52

> Username: anadon  
> Created_at: 2018-09-30 14:47:07 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425725986  

@jzmaddock Thinking on the subgraph stuff more, I think it tends to work like sets and subsets. So a set always contains the items in it's subsets, but the converse is not true.  So then, you can add items to a subset and transitively in the larger set.  New vertexes can be added to the graph but are not added to a subgraph by such an insertion, but adding a new vertex to a subgraph adds it to the graph.  This is the only variation from what you're saying.  I'm not confident what the implementation intends, though.  
  
@pdimov  What about adding other sanitizers?  Could add the following: `-fvtable-verify=preinit -fstack-check -fstack-protector-all -fsanitize-address-use-after-scope -fsanitize=leak -fsanitize=address`?  The bugs `-fsanitize=undefined` seem easy enough to fix and the extra sanitizers should also catch similar difficulty bugs I can work on.

---

## Comment 53

> Username: pdimov  
> Created_at: 2018-09-30 15:06:04 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425727426  

Graph would benefit from `-fsanitize=address`, but I have to get `undefined` working first, which is taking more than expected. Not familiar with the rest.

---

## Comment 54

> Username: anadon  
> Created_at: 2018-09-30 15:27:30 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425728874  

@pdimov What are you running into with `undefined`?

---

## Comment 55

> Username: pdimov  
> Created_at: 2018-09-30 16:07:03 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425731533  

There were false positives caused by `-fvisibility=hidden`, which I needed to work around, and a real issue in Boost.Serialization, for which I had to disable the `serialize.cpp` under UBsan. The results are at https://travis-ci.org/boostorg/graph/builds/435276340, the only remaining failure is a legitimate integer overflow.

---

## Comment 56

> Username: jzmaddock  
> Created_at: 2018-09-30 16:15:37 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425732109  

>Thinking on the subgraph stuff more, I think it tends to work like sets and subsets. So a set always contains the items in it's subsets, but the converse is not true. So then, you can add items to a subset and transitively in the larger set. New vertexes can be added to the graph but are not added to a subgraph by such an insertion, but adding a new vertex to a subgraph adds it to the graph. This is the only variation from what you're saying. I'm not confident what the implementation intends, though.  
  
Yes that's pretty much it, but we have 2 overloads of add_vertex:  
  
```  
vertex_descriptor  
add_vertex(subgraph& g);  
```  
  
Adds a new vertex to the subgraph and all of it's parents, and then the one that we're changing:  
  
```  
vertex_descriptor  
add_vertex(vertex_descriptor u_global, subgraph& g);  
```  
  
Takes an existing vertex from the root graph, and adds it to subgraph `g`.  Logically it makes no sense to call this for the root graph because it's a precondition that `u_global` is in that graph already.

---

## Comment 57

> Username: pdimov  
> Created_at: 2018-09-30 16:42:22 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425733921  

Live results with `-fsanitize=address,undefined` at https://travis-ci.org/boostorg/graph/jobs/435291924.  
  
I had to disable LeakSanitizer because it kept encountering a fatal error: https://travis-ci.org/boostorg/graph/jobs/435286728.

---

## Comment 58

> Username: swatanabe  
> Created_at: 2018-09-30 17:00:54 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-425735155  

AMDG  
  
On 09/30/2018 10:15 AM, jzmaddock wrote:  
> <snip>  
> ```  
> vertex_descriptor  
> add_vertex(vertex_descriptor u_global, subgraph& g);  
> ```  
>   
> Takes an existing vertex from the root graph, and adds it to subgraph `g`.  Logically it makes no sense to call this for the root graph because it's a precondition that `u_global` is in that graph already.  
>   
  
Another way of expressing this is:  
- Pre: The vertex must be present in the root graph.  
- Post: The vertex is present in the subgraph.  
vs.  
- Pre: The vertex must be present in the root graph, but not in g.  
- Post: The vertex was added to the subgraph  
  
The original code seems to follow the latter, while  
the new code obeys the former.  I'm fine with this  
change, provided /the documentation is clarified./  
Simpler is better.  
  
In Christ,  
Steven Watanabe

---

## Comment 59

> Username: jeremy-murphy  
> Created_at: 2018-10-12 09:07:29 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429258480  

> I don't understand why this PR groups several other PRs together. The original PRs should be merged one by one, as needed.  
  
Although I appreciate anyone making an effort, I agree with Peter and really discourage this approach to merging multiple PRs in one go. The primary issue is the conflation of fixes into a single merge, which doesn't allow for reasoning, or changing our mind, about each change independently. The secondary issue is that it might seem expedient to the person that can push to this branch, but, what about all the contributors that opened the original PRs? As far as I could tell, the real blocker to progress was the absence of a maintainer to review and merge the extant PRs, not the absence of one more PR.  
As I said, I really appreciate anyone making an effort, but I struggle to support this way of tackling the backlog.  
No offence meant to anyone, but my sincere suggestion is to close this PR and continue the work on each respective PR independently. There seems to be momentum for it now, and I assume, @jzmaddock , that you have merge permission?

---

## Comment 60

> Username: jzmaddock  
> Created_at: 2018-10-12 11:12:34 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429291155  

>Although I appreciate anyone making an effort, I agree with Peter and really discourage this approach to merging multiple PRs in one go. The primary issue is the conflation of fixes into a single merge, which doesn't allow for reasoning, or changing our mind, about each change independently. The secondary issue is that it might seem expedient to the person that can push to this branch, but, what about all the contributors that opened the original PRs? As far as I could tell, the real blocker to progress was the absence of a maintainer to review and merge the extant PRs, not the absence of one more PR.  
  
That's my fault/suggestion really - the issue is that the library had no CI set up and working - I've fixed that now, but I believe there is no way we can trigger CI runs on old PR's (that pre-date the CI additions) so there's no benchmark for us to judge them by.  There's also no way for us to push to these branches are they're owned by the original submitter.... we can ask for the original submitter to make changes, but if they're trivial it's often easier to just start a new branch and PR for integration testing.  
  
Please prove me wrong on the above - nothing would delight me more ;)  
  
>There seems to be momentum for it now, and I assume, @jzmaddock , that you have merge permission?  
  
Yup, and I think this one is about ready - I just need a bit of time to double check.

---

## Comment 61

> Username: anadon  
> Created_at: 2018-10-12 13:37:20 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429328316  

So do I just close this, then?

---

## Comment 62

> Username: mohe2015  
> Created_at: 2018-10-12 16:24:48 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429382694  

@jzmaddock  
> the issue is that the library had no CI set up and working - I've fixed that now, but I believe there is no way we can trigger CI runs on old PR's  
  
Try to close and reopen the PRs. That MAY trigger a CI run.

---

## Comment 63

> Username: pdimov  
> Created_at: 2018-10-12 16:26:35 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429383222  

It will, but it's better to ask their authors to rebase against the current develop.

---

## Comment 64

> Username: jzmaddock  
> Created_at: 2018-10-12 17:04:45 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429394136  

>It will, but it's better to ask their authors to rebase against the current develop.  
  
Isn't that unnecessary unless there is a merge conflict (in which case the CI tests won't be run anyway)?

---

## Comment 65

> Username: pdimov  
> Created_at: 2018-10-12 17:09:30 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429395489  

I think that pull requests that have been created at a time there was no CI will not be CI-tested, no matter how many times we reopen them. :-)

---

## Comment 66

> Username: pdimov  
> Created_at: 2018-10-12 17:09:57 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429395629  

But I could be wrong about this.

---

## Comment 67

> Username: jzmaddock  
> Created_at: 2018-10-12 17:13:46 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429396653  

> But I could be wrong about this.  
  
You are - it worked on the one I just tried :)

---

## Comment 68

> Username: jzmaddock  
> Created_at: 2018-10-12 17:34:56 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429402681  

Merged to develop.  
  
I've also re-instated the removed assert as per @swatanabe comment.

---

## Comment 69

> Username: anadon  
> Created_at: 2018-10-12 18:16:08 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429414167  

@jzmaddock Holy inbox batman!  I will ping you for small stuff from now on.  Suddenly all the graph.

---

## Comment 70

> Username: jeremy-murphy  
> Created_at: 2018-10-12 21:27:38 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429467459  

I hope this works out as much as anyone, and I thank @anadon for their effort, but if something is mysteriously wrong and requires reversion of this merge and going back to square one, I will say "I told you so".  :)

---

## Comment 71

> Username: anadon  
> Created_at: 2018-10-12 22:28:26 UTC  
> Url: https://github.com/boostorg/graph/pull/114#issuecomment-429479905  

@jeremy-murphy I know...

---
