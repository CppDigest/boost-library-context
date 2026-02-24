# #81 Examples: Use modern C++ [Open]

> Username: murraycu  
> Created at: 2016-11-02 11:37:02 UTC  
> Updated at: 2024-12-15 16:37:44 UTC  
> Url: https://github.com/boostorg/graph/pull/81  

These commits change the code in examples/ to use modern C++, specifically C++11. For instance, to use auto to avoid the verbosity of the traits and typedefs.  
  
Even if we don't want to require C++11 for the API itself, maybe it's OK to use it for the example code, to more clearly demonstrate the API. I would understand if you don't want this.  
  
The last commit ("Add and use make_range_pair") is even more debatable: It uses a range_pair utility class to allow use of range-based for loops, but maybe this should actually be a change to the API, or maybe it's something that will be better one day with C++ ranges in C++20.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2016-11-05 19:22:41 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-258634841  

I'm currently disinclined to apply this PR though I do appreciate your efforts Murray.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2018-07-25 10:15:05 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-407705969  

I would love to see Boost.Graph get a modernization, I would actually vote for C++14 at this point.  
Since Boost.Graph is not depended on by other libraries in Boost but it has been around for a long time, what would it take for a modernization to be accepted?

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-08-22 13:18:28 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-415028508  

@Belcourt why are (were) you disinclined?  You rejected the PR but gave no reason for the submitter to allow for more improvement.

---

## Comment 4

> Username: anadon  
> Created_at: 2018-08-31 18:21:12 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-417750328  

I'm helping out with the PR backlog.  Looks like you have a large code change.  I would like to merge this in around v1.69.  The modernization is greatly appreciated, but making some fixes available and then having this change at a later version.  There is a very long mailing thread where it looks like the latest when C++03 support will be thought about is 2020, but I'm comfortable telling people now that they should be using a newer system.  This can affect other PRs, so lets hold off on merging everything now.  This is to let you know and help me prioritize PR's.

---

## Comment 5

> Username: anadon  
> Created_at: 2018-10-15 19:53:09 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-429990266  

Should we actually make this a branch for devel-cpp17?

---

## Comment 6

> Username: murraycu  
> Created_at: 2018-11-01 08:29:09 UTC  
> Updated_at: 2018-11-01 08:29:26 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-434969549  

> I'm helping out with the PR backlog. Looks like you have a large code change. I would like to merge this in around v1.69. The modernization is greatly appreciated, but making some fixes available and then having this change at a later version.   
[snip]  
  
@anadon That's encouraging. Thanks. I don't quite understand what you'd like to do and when. Please feel free to request changes or updates from me that would help you to get this merged into the real code base.

---

## Comment 7

> Username: anadon  
> Created_at: 2018-11-01 14:13:39 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-435053141  

@murraycu Can you please rebase this PR on upstream's devel branch?

---

## Comment 8

> Username: murraycu  
> Created_at: 2018-11-01 22:26:33 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-435208006  

OK. I've rebased on master and force pushed to the develop-murrayc-examples-modern-cpp branch in my fork.  
  
I can't seem to remember how I built this at the time, inside modular-boost, so it might not actually build now.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-11-02 13:29:07 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-435380290  

@murraycu Looks like there are some CI failures.  Could you please take a look at those?

---

## Comment 10

> Username: anadon  
> Created_at: 2018-11-05 20:58:06 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-436032165  

2 failures are in the code changes, 2 are from external parameter parsing.  Shouldn't be that bad to fix.

---

## Comment 11

> Username: murraycu  
> Created_at: 2018-11-29 13:08:59 UTC  
> Updated_at: 2018-11-29 13:09:25 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-442827342  

At the moment I can't even build the regular develop branch:  
  
```  
[murrayc@murrayc-ThinkPad-X220 boost (develop *)]$ ./b2   
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
error: at /home/murrayc/checkout/github/boost/tools/build/src/kernel/modules.jam:107  
error: Cannot create link boost/pending/property_serialize.hpp to libs/graph/include/boost/pending/property_serialize.hpp.  
error: Link previously defined to another file, libs/graph_parallel/include/boost/pending/property_serialize.hpp.  
```

---

## Comment 12

> Username: murraycu  
> Created_at: 2018-11-29 19:52:35 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-442970439  

The remaining build errors in CI are in tests/isomorphism.cpp, and seem to be unrelated to my changes.

---

## Comment 13

> Username: anadon  
> Created_at: 2018-12-04 14:48:16 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-444125510  

I've been out a while, but I think an external dependency changes and all  
of graph broke.  I'd lets things settle while graph gets fixed back up.  
  
On Thu, Nov 29, 2018 at 2:52 PM Murray Cumming <notifications@github.com>  
wrote:  
  
> The remaining build errors in CI are in tests/isomorphism.cpp, and seem to  
> be unrelated to my changes.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/81#issuecomment-442970439>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC-CBbJV6rHErOodtiziho7-JN2SmtcUks5u0DsEgaJpZM4KnIyE>  
> .  
>

---

## Review 14 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-01-03 23:37:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/81#pullrequestreview-1803231422  

📁 example/range_pair.hpp

```diff
  14 |+ // https://cplusplusmusings.wordpress.com/2013/04/14/range-based-for-loops-and-pairs-of-iterators/
  15 |+ template <typename T_Pair>
  16 |+ class range_pair
```

> Username: jeremy-murphy  
> Created_at: 2024-01-03 23:37:51 UTC  
> Url: https://github.com/boostorg/graph/pull/81#discussion_r1441096262  

I don't think this class is a good idea, sorry. For starters, it's not how the library is actually used. Secondly, there must be something equivalent in Boost.Range already, presumably `iterator_range`.  
  
Thirdly, and more importantly, the best thing to do would be to add `begin(boost::pair)`/`end(boost::pair)` functions to `graph_traits.hpp` or some other common file so that `boost::pair` is automatically treated as a range. If that causes problems, an alternative would be to return `iterator_range` instead of `boost::pair`.  
  
Now, that should be done separately from this PR so that this remains entirely about updating examples.  
  
Finally -- the reason I am suddenly interested in this, is we have now effectively dropped support for pre-C++11, so we can actually consider merging!


---

## Comment 15

> Username: murraycu  
> Created_at: 2024-12-15 16:37:43 UTC  
> Url: https://github.com/boostorg/graph/pull/81#issuecomment-2543940973  

>  we have now effectively dropped support for pre-C++11, so we can actually consider merging!  
  
OK. Rebasing this is a bit awkwad after the reformatting in 2019, so I'll make a series of smaller pull requests instead.

---
