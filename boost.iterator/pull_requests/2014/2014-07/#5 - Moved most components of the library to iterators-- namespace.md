# #5 Moved most components of the library to iterators:: namespace. [Merged]

> Username: Lastique  
> Created at: 2014-07-02 20:39:10 UTC  
> Updated at: 2014-07-19 16:19:38 UTC  
> Merged at: 2014-07-10 17:33:50 UTC  
> Closed at: 2014-07-10 17:33:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/5  

This change excludes boost:: and boost::detail:: namespaces from ADL for unqualified function calls with iterators in arguments (e.g. algorithms). This reduces the possibility of name clashes with other libraries and user's code. One of the effects should be fixing test failures on gcc 4.2 and 4.4 due to clashed with Boost.TypeTraits.  
  
Also some of the functions marked with inline keyword.  
  
Note 1: I'm not sure that all names are needed in the global boost:: namespace. In particular, some names in iterator_archetypes.hpp look like implementation details, even though reside in the public namespace.  
  
Note 2: I did not move pointee<> and indirect_reference<> to iterators namespace because these tools looked loosely related to iterators. These tools could potentionally be moved to another library.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-07-02 20:42:07 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-47833302  

To clarify, I cannot verify that the change actually fixes the tests on Darwin 4.2.1 and qcc 4.4.2 but it should.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-07-02 21:03:18 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-47836306  

Also, the test failures are a regression since https://github.com/boostorg/iterator/pull/4 was merged.

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-07-05 19:54:17 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-48094995  

I managed to run the tests locally on gcc 4.4, all tests passed.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2014-07-14 11:51:40 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-48891099  

That change broke Boost.Range. In demote_iterator_traversal_tag.hpp, it uses boost::detail::pure_traversal_tag, which now has to be boost::iterators::detail::pure_traversal_tag.

---

## Comment 5

> Username: Lastique  
> Created_at: 2014-07-14 12:04:12 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-48892009  

Note that pure_traversal_tag is an implementation detail. If Boost.Range needs it, it should probably be moved into public namespace (boost::iterators).

---

## Comment 6

> Username: mclow  
> Created_at: 2014-07-14 23:38:56 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-48973962  

If this is "develop only", I have no problem with this. If it's going to end up on "master" before the 1.56 release, then I am concerned, especially with the Boost.Range breakage.

---

## Comment 7

> Username: Lastique  
> Created_at: 2014-07-15 03:04:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-48985490  

We can delay merging this to master until after the release, there's no rush.

---

## Comment 8

> Username: mclow  
> Created_at: 2014-07-15 03:05:40 UTC  
> Url: https://github.com/boostorg/iterator/pull/5#issuecomment-48985532  

That's fine with me; thanks.

---
