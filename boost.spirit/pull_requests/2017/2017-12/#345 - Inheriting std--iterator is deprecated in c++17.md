# #345 Inheriting std::iterator is deprecated in c++17 [Merged]

> Username: DanielaE  
> Created at: 2017-12-29 15:46:55 UTC  
> Updated at: 2018-01-14 17:11:24 UTC  
> Merged at: 2018-01-01 16:49:29 UTC  
> Closed at: 2018-01-01 16:49:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/345  

Boost's iterator.hpp is deprecated, too. It does nothing but pulling std::iterator into namespace boost and including standard headers 'iterator' and 'cstddef'. Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2017-12-29 16:37:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/345#pullrequestreview-85982846  

📁 include/boost/spirit/home/classic/iterator/multi_pass.hpp

```diff
 779 |-         typename input_t::reference
 780 |-     > type;
 772 |+     struct type {
```

> Username: Kojoley  
> Created_at: 2017-12-29 16:37:48 UTC  
> Updated_at: 2017-12-29 18:36:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/345#discussion_r159080636  

It looks like the comment above is obsolete because of https://github.com/boostorg/core/commit/bca30b2ec7f41e6ece057b46d1358f0506c904d3.

> Username: DanielaE  
> Created_at: 2017-12-29 17:37:20 UTC  
> Updated_at: 2017-12-29 18:36:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/345#discussion_r159085946  

No, it's not. Because of the change by that commit, inheriting from boost::iterator or referring to its members is just the same as inheriting from or using std::iterator. This is deemed deprecated now since C++17 and the latest version of MSVC spits out nasty warnings because of that. On top of that, Boost's version of iterator.hpp is deprecated as well.

> Username: Kojoley  
> Created_at: 2017-12-29 17:52:57 UTC  
> Updated_at: 2017-12-29 18:36:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/345#discussion_r159087258  

```  
 // Meta-function to generate a std::iterator<> base class for multi_pass. This  
 //  is used mainly to improve conformance of compilers not supporting PTS  
 //  and thus relying on inheritance to recognize an iterator.  
```  
I do not think this part makes any sense after the change.  
  
> This is deemed deprecated now since C++17 and the latest version of MSVC spits out nasty warnings because of that. On top of that, Boost's version of iterator.hpp is deprecated as well.  
  
Yes, I understand and agree with the PR. I just think that the comment must be updated too.  
Will leave this decision to @djowel,

> Username: DanielaE  
> Created_at: 2017-12-29 18:31:11 UTC  
> Updated_at: 2017-12-29 18:36:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/345#discussion_r159090641  

Ah, *that's* your objection. Nevermind - I'll change that asap.


---

## Comment 2

> Username: djowel  
> Created_at: 2017-12-29 19:30:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/345#issuecomment-354490309  

👍 LGTM

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-01-01 16:49:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/345#issuecomment-354663180  

Thanks!

---
