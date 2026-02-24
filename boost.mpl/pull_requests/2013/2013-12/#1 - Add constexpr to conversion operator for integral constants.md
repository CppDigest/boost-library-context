# #1 Add constexpr to conversion operator for integral constants [Closed]

> Username: pfultz2  
> Created at: 2013-12-29 04:39:38 UTC  
> Updated at: 2014-06-17 01:44:29 UTC  
> Closed at: 2014-05-16 13:51:09 UTC  
> Url: https://github.com/boostorg/mpl/pull/1  

I added constexpr to conversion operator for integral constants, this will make type traits more closely match the type traits in C++14.

---

## Comment 1

> Username: ahmedcharles  
> Created_at: 2014-05-16 05:54:30 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-43299634  

I would be willing to merge this if it had a test case, like:  
  
constexpr bool b = boost::is_integral<int>();  
static_assert(b, "some text");

---

## Comment 2

> Username: pfultz2  
> Created_at: 2014-05-16 06:43:07 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-43301930  

Alright, I added test cases.

---

## Comment 3

> Username: ahmedcharles  
> Created_at: 2014-05-16 13:51:09 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-43333154  

I pushed this after rebasing it, so I'll close. Thanks for the tests.

---

## Comment 4

> Username: gnzlbg  
> Created_at: 2014-05-30 14:49:58 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44659356  

Has this been merged to master?   
  
I just sent a fix that should have been already fixed in this patch (see #7).

---

## Comment 5

> Username: gnzlbg  
> Created_at: 2014-05-30 15:04:50 UTC  
> Updated_at: 2014-05-30 15:13:27 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44661024  

@robertramey replied on Mar 27  
  
> Why is this change necessary? Integral constants don't need to be constant expressions - const (which they already are) is enough. [...]  
  
Integral constants _are_ intended to be used for compile-time computations.  Unless they provide a `constexpr` interface they can't be used within `constexpr` functions in C++11/14.   
  
> I don't see what this change would fix or improve - and it's very hard to anticipate possible surprises.  
  
This allows you to, e.g., `fold` over a `boost::mpl::range_c` range inside a constexpr function like this:  
  
`fold(boost::mpl::range_c<int, 0, 10>{}, 0, my_constexpr_computation_object{});`  
  
Unfortunately, one cannot use lambdas within constexpr functions yet, so it feels a bit C++03.

---

## Comment 6

> Username: pfultz2  
> Created_at: 2014-05-30 16:17:12 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44669443  

@gnzlbg It has been merged into develop, but not into master yet.   
  
Also, I believe pull requests should be off of develop, not master branch. (see [here](http://boost.2283326.n4.nabble.com/assign-Merge-request-for-type-disambiguation-with-C-11-td4661617.html)) I'm not sure why, it shouldn't matter, but it seems like boost is still new to the git branching model.

---

## Comment 7

> Username: gnzlbg  
> Created_at: 2014-05-31 01:16:56 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44714324  

@pfultz2 thanks I didn't saw it.  
  
When I update my local git I only update master branches..  
  
If something is unfinished it should be in develop, but this pull-request seems pretty finished, so I don't see the point into merging it on develop and not on master.. I could have benefitted from it a lot today morning.

---

## Comment 8

> Username: mclow  
> Created_at: 2014-05-31 04:29:59 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44717704  

The general idea is to fix things on develop, let the tests cycle, then merge to master.

---

## Comment 9

> Username: gnzlbg  
> Created_at: 2014-05-31 11:07:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44745418  

@mclow marshall do you know if there is a way to check out the develop branches for every library in modular-boost instead of the master branches?

---

## Comment 10

> Username: mclow  
> Created_at: 2014-05-31 14:24:50 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44749593  

I believe that `git submodule foreach git checkout develop` should do it.

---

## Comment 11

> Username: gnzlbg  
> Created_at: 2014-06-02 08:13:41 UTC  
> Url: https://github.com/boostorg/mpl/pull/1#issuecomment-44811613  

@mclow thanks marshall, works like charm.

---
