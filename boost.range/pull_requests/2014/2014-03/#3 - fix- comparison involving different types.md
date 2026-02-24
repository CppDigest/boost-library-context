# #3 fix: comparison involving different types [Closed]

> Username: gnzlbg  
> Created at: 2014-03-11 10:14:19 UTC  
> Updated at: 2014-07-12 14:56:30 UTC  
> Closed at: 2014-06-09 22:19:53 UTC  
> Url: https://github.com/boostorg/range/pull/3  

difference_type does not necessary need to be of type int or size_t

---

## Comment 1

> Username: gnzlbg  
> Created_at: 2014-06-10 09:52:32 UTC  
> Updated_at: 2014-06-10 10:03:13 UTC  
> Url: https://github.com/boostorg/range/pull/3#issuecomment-45594387  

@neilgroves I just went through the develop branch and found:  
  
``` c++  
BOOST_ASSERT(at >= 0);  
BOOST_ASSERT(static_cast<typename base_type::size_type>(at) < size());  
```  
  
This code still assumes that `at` is implicitly convertible to int (in the first assert).  
  
Marshall Clow clarified me that:  
  
> The standard says that an iterator’s difference_type must be a ‘signed integer type” section 24.2.1 [iterator.requirements.general]  
>   
> These types are defined in section 3.9.1 [basic.fundamental], and seems to me to imply that it has to be either one of  “signed char”, “short int”, “int”, “long int”, and “long long int”, or an “extended signed integer type” provided by the implementation (which means the compiler and/or standard library). I don’t see any provision here for an iterator having a difference_type that is a user-defined type.  
  
My "fix" goes thus beyond what the standard requires. It allows user-defined difference types that are explicitly convertible, but not implicitly convertible, to the standard signed integer types.   
  
I've been using those types with Boost.Range successfully by applying this patch to Range at each release, and by compiling in production in those clusters where I can't patch Range myself.

---

## Comment 2

> Username: neilgroves  
> Created_at: 2014-06-10 10:03:07 UTC  
> Url: https://github.com/boostorg/range/pull/3#issuecomment-45595283  

Hi!  
  
I believe the standard states that difference type must be a signed  
integral type. Therefore the promotion rules should always make this  
comparison safe.  
  
Do yoy have an example that is hindered by this? I'm okay to make a  
conforming change to support use cases where the iterator isn't strictly  
conforming.  
  
I would like to know if you have interpreted the standard differently. This  
is largely just to improve my understanding.  
  
I'm busy today during the day but hope to get to this in the evening.  
  
Regards,  
Neil  
On 10 Jun 2014 10:52, "gnzlbg" notifications@github.com wrote:  
  
> @neilgroves https://github.com/neilgroves I just went through the  
> develop branch and found:  
>   
> BOOST_ASSERT(at >= 0);BOOST_ASSERT(static_cast<typename base_type::size_type>(at) < size());  
>   
> This code still assumes that at is implicitly convertible to int (in the  
> first assert).  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/range/pull/3#issuecomment-45594387.

---

## Comment 3

> Username: gnzlbg  
> Created_at: 2014-06-10 10:18:08 UTC  
> Url: https://github.com/boostorg/range/pull/3#issuecomment-45596544  

@neilgroves i've updated my comment above (I guess it only shows through github), sorry about that.  
  
Thanks for your hard work!  
  
I have to add that libc++ rejected a similar patch since it is not required by the standard. Boost can, in my opinion, go beyond the standard here and maybe even serve as basis for an improvement draft.  
  
My use-case is an octree data structure where I offer different types of indexes (and iterators) to traverse the tree (dfs, bfs, child, neighbors,...). Since the meaning of the indices (and iterators) differs, I decided to make them explicitly convertible to `int` (to allow hacking) but not implicitly convertible (strongly typed). This produces nice compiler error messages when you use them wrong (e.g. using a child index to access a neighbor within an array of neighbors). Every now and then one needs to offset an index by the distance between a pair of iterators. A strongly typed `difference_type` prevents errors in that situation.  
  
C++ is arguably not the best language for this and I might be a bit paranoid, but every time I hit a compiler error when writing user code I am really glad I went this way. Strongly typed interfaces like this have a 0% false positive rate.

---
