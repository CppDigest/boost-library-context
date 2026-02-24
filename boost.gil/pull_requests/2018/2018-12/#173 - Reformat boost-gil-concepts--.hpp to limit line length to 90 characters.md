# #173 Reformat boost/gil/concepts/*.hpp to limit line length to 90 characters [Merged]

> Username: mloskot  
> Created at: 2018-12-06 16:43:02 UTC  
> Updated at: 2018-12-06 22:58:03 UTC  
> Merged at: 2018-12-06 18:11:59 UTC  
> Closed at: 2018-12-06 18:11:59 UTC  
> Url: https://github.com/boostorg/gil/pull/173  

Replace `typedef` with using declaration.  
Replace complex return type declared for functions with trailing return type and `auto`.  
Format complex metaprogramming constructs in clear and readable way.  
Remove superfluous `public` access specifier from `struct` inheritance.  
Rename ambiguous type aliases.  
Make template parameters  
- upper-case (if initials e.g. CS)  
- camel-case if multi-word (e.g. ColorSpace).  
  
### References  
  
Part of ongoing extensive modernisation, reformatting and clean-up prior being applied, also as preparation for overhaul removing/replacing with C++11 Boost.MPL, Boost.TypeTraits and other tightly coupled Boost libraries as dependencies.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-12-06 16:50:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/173#pullrequestreview-182339401  

📁 include/boost/gil/concepts/color_base.hpp

```diff
 132 |-         typedef typename kth_element_const_reference_type<ColorBase, num_elements - 1>::type CR;
 146 |+         using TN = typename kth_element_type<ColorBase, num_elements - 1>::type;
 147 |+         using RN = typename kth_element_const_reference_type<ColorBase, num_elements - 1>::type;
```

> Username: stefanseefeld  
> Created_at: 2018-12-06 16:50:20 UTC  
> Url: https://github.com/boostorg/gil/pull/173#discussion_r239528808  

I was expecting to review reformatting changes only. Here you are changing a (public) member name from 'CR' to 'RN'. I assume you checked that this isn't used anywhere, right ?  
But even if it isn't used, isn't it part of the public API, i.e. could some downstream code be affected ?

> Username: mloskot  
> Created_at: 2018-12-06 17:25:51 UTC  
> Url: https://github.com/boostorg/gil/pull/173#discussion_r239542235  

> I was expecting to review reformatting changes only.  
  
Yes, sorry. After long dumb refactoring moments, my brain strived for distraction :-)  
  
> Here you are changing a (public) member name from 'CR' to 'RN'.   
> But even if it isn't used, isn't it part of the public API, i.e. could some downstream code be affected ?  
  
AFAICT, those are local type aliases.  
  
Even if those were not local to the `constraints` function, they would be local to the definition of constraint class which is supposed to be aligned with the very basic and unified interface, since we use the old ConceptCheck style, we do:  
  
_"define a member function named constraints() in which we will exercise the valid expressions of the concept"_ ~http://boost.sourceforge.net/libs/concept_check/creating_concepts.htm  
  
  
> I assume you checked that this isn't used anywhere, right ?  
  
Yes, I'm building all tests with `-DBOOST_GIL_USE_CONCEPT_CHECK=1` (undefined by default).  
  
There is one bug, but it is unrelated to changes in this particular PR:  
  
```  
boost/gil/concepts/pixel_iterator.hpp:87:41: error: ‘memunit_step’ was not declared in this scope  
boost/gil/concepts/pixel_iterator.hpp:90:30: error: ‘memunit_advanced’ was not declared in this scope  
...  
```  
  
In fact, none of the `memunit_*` functions are (forward) declared in any way. AFAICT, it has **never** been :-)  
  
https://github.com/boostorg/gil/blob/boost-1.35.0/include/boost/gil/gil_concept.hpp  
https://github.com/boostorg/gil/blob/boost-1.60.0/include/boost/gil/gil_concept.hpp  
https://github.com/boostorg/gil/blob/boost-1.65.0/include/boost/gil/gil_concept.hpp

> Username: mloskot  
> Created_at: 2018-12-06 17:31:30 UTC  
> Url: https://github.com/boostorg/gil/pull/173#discussion_r239544192  

I've opened a bug report about it #174


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-12-06 16:52:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/173#pullrequestreview-182340547  

📁 include/boost/gil/concepts/detail/type_traits.hpp

```diff
  15 | template <typename T>
  16 | struct remove_const_and_reference
  17 |-     : public ::boost::remove_const<typename ::boost::remove_reference<T>::type>
```

> Username: stefanseefeld  
> Created_at: 2018-12-06 16:52:23 UTC  
> Updated_at: 2018-12-06 16:52:24 UTC  
> Url: https://github.com/boostorg/gil/pull/173#discussion_r239529690  

It's tempting to suggest to replace this use of boost type_traits by std type_traits. I take it you plan to do that in a separate PR, yes ? :-)

> Username: mloskot  
> Created_at: 2018-12-06 16:59:00 UTC  
> Url: https://github.com/boostorg/gil/pull/173#discussion_r239532634  

Yes, in this particular case it should be possible, but I have not even looked at what trait are being used here.  
  
A couple of attempts to get rid of Boost.TypeTraits failed and now I'm planning to take another look soon. So, yes, this will be removed in separate PR in near future :-)


---

## Review 3 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-12-06 17:28:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/173#pullrequestreview-182357464  

OK (once all test runs finish successfully, of course :-) )

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-12-06 18:11:30 UTC  
> Url: https://github.com/boostorg/gil/pull/173#issuecomment-444972458  

@stefanseefeld Thank you!

---
