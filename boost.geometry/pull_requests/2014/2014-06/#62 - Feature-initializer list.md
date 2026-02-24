# #62 Feature/initializer list [Merged]

> Username: awulkiew  
> Created at: 2014-06-16 17:10:46 UTC  
> Updated at: 2015-01-10 00:12:19 UTC  
> Merged at: 2015-01-10 00:12:02 UTC  
> Closed at: 2015-01-10 00:12:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/62  

This adds support for std::initializer_list for some of the models (see the test for example usage).  
model::point's constructor is no longer explicit to allow using C++11 unified initialization syntax.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-17 12:40:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/62#issuecomment-46301075  

There is a problem with ctors taking std::initializer_list and Boost.Assign, e.g. see:  
http://lists.boost.org/Archives/boost/2013/12/209370.php  
I reproduced it on msvc-12.0. On GCC 4.8.1 the test compiles. I expect that clang also compiles it.  
Since Boost.Assign is widely used in the examples I'm guessing that we should be backward compatible. Anyway, we could:  
1 enable this feature for some compilers,  
2 disable for some,  
3 think about some workaround,  
4 don't add this feature,  
5 add but don't do anything about Boost.Assign.  
  
ad 3. Possible options are:  
- ctors and assignments taking 1 parameter of any type assumed to be a Range (this could probably cause problems with copy ctor and assignment operator)  
- ctors and assignments taking assign_detail::converter<>  
- the implementation of some proxy class handling any conversion + ctors and assignments (it could be included as an additional file, e.g. with the specialization for converter<>)  
- something else  
  
ad 5. On platforms on which the test fails people won't be able to use Boost.Assign with STD containers. And I'm guessing that if someone is using Boost.Assign with BG models he's using it also with STD containers. So he'd be forced to not use it at all, change the compiler or wait for a newer version.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-01-02 23:45:23 UTC  
> Updated_at: 2015-01-02 23:56:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/62#issuecomment-68573838  

I believe the support for initializer lists is ready for merging. To allow using Boost.Assign and therefore ensure backward-compatibility I commented-out the assignment operators taking `std::initializer_list<>`. I tested it on msvc (10, 12), gcc (4.8, 4.9) and clang (3.4).  
  
Currently when an initializer list is assigned to one of the geometries, first a geometry object should be created using a ctor taking `std::initializer_list<>` and then it should be moved using implicitly defined move assignment operator.  
  
If Boost.Assign was somehow fixed (there are 2 tickets for this https://svn.boost.org/trac/boost/ticket/5419 4yo and https://svn.boost.org/trac/boost/ticket/7364 2yo) or the support for Assign was dropped on C++11 compilers (since Assign doesn't work with STL containers anyway), assignment operators taking `std::initializer_list<>` could be restored.  
  
EDIT: actually one of the tickets is about assignment operator taking `std::initializer_list<>` and the other one about passing Assign's `generic_list` into the Container's ctor.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-01-10 00:11:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/62#issuecomment-69424485  

I've shielded the support with macro and enabled it for one test, testing together `std::initializer_list<>`, uniform initialization syntax and Boost.Assign's `list_of()`, `pair_list_of()` and `tuple_list_of()`. This way we would be able to check if everything works before enabling the support for the users.

---
