# #12 Move assignment support [Closed]

> Username: DenizThatMenace  
> Created at: 2016-07-21 10:52:09 UTC  
> Updated at: 2018-03-17 16:26:05 UTC  
> Closed at: 2017-03-30 17:06:49 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12  

Extends _Boost.TypeErasure_ to support **move-assignment** when compiling with support for rvalue-references (aka with C++11 or C++14 enabled).  
  
This was successfully **tested on Linux** with **Clang** (versions 3.3 to 3.6 and 3.8) as well as with **GCC** (version 4.6 to 6.1) with C++98, C++11 and C++14 (where supported by compiler).  
- GCC 4.6 does not support C++11 (enough) so that it cannot be enabled, which means that move-assignment support is not available for that compiler.  
- GCC 4.7 and 4.8 have some problematic/wrong C++11 implementations, so that in some situations they complain about ambiguities were other compilers do not have any problems. (Such test-cases are disabled for these compilers.)  
  
The associated Trac-ticket is: [12337](https://svn.boost.org/trac/boost/ticket/12337)  
  
**Note:**  
This pull-request builds on top of some fixes separately provided as pull-requests [9](https://github.com/boostorg/type_erasure/pull/9) and [10](https://github.com/boostorg/type_erasure/pull/10).

---

## Comment 1

> Username: swatanabe  
> Created_at: 2016-07-21 16:53:46 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-234315123  

AMDG  
  
On 07/21/2016 04:52 AM, Deniz Bahadir wrote:  
  
> Extends _Boost.TypeErasure_ to support **move-assignment** when compiling with support for rvalue-references (aka with C++11 or C++14 enabled).  
  
  I would actually prefer to have a common template  
for all assignment and then have both assignable  
and move_assignable be implemented using it  
(similar to constructible).  
  
Ideally it would be something like:  
template<class T, class U = T>  
using copy_assignable = assignable<T, const U&>;  
template<class T, class U = T>  
using move_assignable = assignable<T, U&&>;  
  
but I've already used the name assignable,  
so backwards compatibility would be an issue.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2016-07-25 16:54:19 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-235013342  

Hi Steven,  
  
originally, I tried to extend `assignable` to support move-assignment, however I ran into ambiguity-problems. That's why I switched to implementing the additional `move_assignable` concept.  
  
However, after rethinking my implementation I think I was successful in integrating my `move_assignable` concept into `assignable` and even keeping the backward-compatibility.  
  
I hope, these changes are closer to what you had in mind.  
  
Thanks,  
Deniz  
  
PS: Possibly, some template-aliases (or derived concepts) `move_assignable` and `copy_assignable` should be provided by the library, too, as in your example above.

---

## Comment 3

> Username: DenizThatMenace  
> Created_at: 2017-01-26 17:25:46 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-275451994  

@swatanabe   
  
Hi Steven,  
I just wanted to bring your attention back to this pull-request.  
  
Is my latest commit close enough to what you had in mind?  
  
We have been using this latest version of the move-assignable extension successfully for half a year now and did not have any problems with it.  
Is there something which still prevents it from being integrated into the official _Boost.TypeErasure_ code?  
  
Thanks,  
Deniz

---

## Comment 4

> Username: swatanabe  
> Created_at: 2017-03-24 18:00:30 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-289099986  

I'm in the process of merging this.  A few notes:  
  
- I don't understand the purpose of 40b52156e7322cb2f146e38653394388507e08c6  
  R or void in the function type passed to extract_concept is entirely ignored.  I'm  
  using a function type here solely as a convenient way to pass a type list.  Also  
  _boost_type_erasure_extract_table is used solely from the constructor which doesn't  
  attempt perfect forwarding (as it is entirely unnecessary), so making it take  
  rvalue references is quite useless.  
- It looks like this implementation leaves assignable<T, U> and assignable<T, const U&>  
  as distinct concepts.  They are functionally identical should be folded together.  
- assignable<T,U&> should not use const U&.  If someone explicitly asks for a  
  non-const reference, then that's what he should get.  
- The documentation was not updated for the latest version.  It still names move_assignable.  
- operator=(U&&) should subsume operator=(const U&).  There's no reason to  
  create multiple overloads when one suffices.  
- assignable should not prevent assignment of raw values.

---

## Comment 5

> Username: DenizThatMenace  
> Created_at: 2017-03-24 20:31:07 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-289135273  

It's been some time since I looked into the code of _Boost.TypeErasure_ and created the patches. However, I hope I can still answer most of your questions.  
  
> * I don't understand the purpose of 40b5215  
R or void in the function type passed to extract_concept is entirely ignored. I'm  
using a function type here solely as a convenient way to pass a type list. Also  
_boost_type_erasure_extract_table is used solely from the constructor which doesn't  
attempt perfect forwarding (as it is entirely unnecessary), so making it take  
rvalue references is quite useless.  
  
If I remember correctly (and as my commit message says), without this change errors occurred if `BOOST_NO_CXX11_VARIADIC_TEMPLATES` was not defined.  
At least, I remember compiling all tests with different compilers (as mentioned in my first comment of this pull-request), with different C++-standard versions and with all four combinations of `BOOST_NO_CXX11_VARIADIC_TEMPLATES` and `BOOST_NO_CXX11_RVALUE_REFERENCES` defined/undefined. Probably, at least one of these combinations failed if 40b5215 was missing.  
  
> * It looks like this implementation leaves assignable<T, U> and assignable<T, const U&>  
as distinct concepts. They are functionally identical should be folded together.  
  
I see what you mean and you are right. However, I am not sure if this is so easy to do.  
In general I always provided specializations for arguments of `U&` and `const U&` if `U&&` was implemented to prevent wrong overload resolution. (A "universal reference" `U&&` - as Scott Meyers calls them - is very often the better match if the types to not match exactly.)  
So here, if no specialization for `const U&` was provided always the specialization for `U&&` might have been chosen instead of the base template. (Or was it the other way around? I don't really know right now.)  
  
> * assignable<T,U&> should not use const U&. If someone explicitly asks for a  
non-const reference, then that's what he should get.  
  
You might be right there. I am not sure why I wrote it like this. Probably because I thought that the right-hand side of an assignment is either an rvalue or will not change during assignment (and is therefore const).   
  
> * The documentation was not updated for the latest version. It still names move_assignable.  
  
I am sorry, that could be true. Probably an oversight.  
I was never able to generate the documentation so I never really was sure if I edited it so that it appears correctly.  
  
> * operator=(U&&) should subsume operator=(const U&). There's no reason to  
create multiple overloads when one suffices.  
  
That, however, did not work in all cases. I do not remember in particular but in general I tried that only rvalue-references use the overloads with `U&&` as argument so that I could further use `std::move` without accidentally moving an lvalue-reference. (While implementing these patches it occurred too often that the wrong overloads were taken later because I no longer had an rvalue. So this was an easy fix for it.)  
  
> * assignable should not prevent assignment of raw values.  
  
Does it? I am not sure if this was newly introduced. I just remember having sometimes weird results with my test-cases until I finally accepted that some kind of assignment does not seem to work or be allowed.  
I am not completely sure but I think move-constructing an `any` is not fully supported either and therefore some kind of assignments fail, too. But I could be wrong.  
  
Only thing I know for sure is that even with this patch type-erased move-only types (like `std::unique_ptr`) cannot be moved into `std::vector<boost::type_erasure<...>>`. Instead I have to create the vector upfront with the final number of elements and then overwrite each element through move-assignment.  
  
  
I had some local test-cases in a single cpp-file that I used to also test my changes with. It is quite a mess but might still be helpful. Therefore I attached it. [type_erasure_move-assignable_test.cpp.zip](https://github.com/boostorg/type_erasure/files/869085/type_erasure_move-assignable_test.cpp.zip)

---

## Comment 6

> Username: swatanabe  
> Created_at: 2017-03-24 21:13:43 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-289144429  

AMDG  
  
On 03/24/2017 02:31 PM, Deniz Bahadir wrote:  
>   
>> * It looks like this implementation leaves assignable<T, U> and assignable<T, const U&>  
> as distinct concepts. They are functionally identical should be folded together.  
>   
> I see what you mean and you are right. However, I am not sure if this is so easy to do.  
> In general I always provided specializations for arguments of `U&` and `const U&` if `U&&` was implemented to prevent wrong overload resolution. (A "universal reference" `U&&` - as Scott Meyers calls them - is very often the better match if the types to not match exactly.)  
> So here, if no specialization for `const U&` was provided always the specialization for `U&&` might have been chosen instead of the base template. (Or was it the other way around? I don't really know right now.)  
>   
  
  My intent is that assignable<T, U> and assignable<T, const U&>  
should be interchangeable.  It's actually pretty easy to do,  
if a bit weird:  
  
template<class T, class U>  
struct assignable : mpl::vector<assignable<T, const U&> > {};  
  
>> * operator=(U&&) should subsume operator=(const U&). There's no reason to  
> create multiple overloads when one suffices.  
>   
> That, however, did not work in all cases. I do not remember in particular but in general I tried that only rvalue-references use the overloads with `U&&` as argument so that I could further use `std::move` without accidentally moving an lvalue-reference. (While implementing these patches it occurred too often that the wrong overloads were taken later because I no longer had an rvalue. So this was an easy fix for it.)  
>   
  
std::forward is supposed to solve that.  
  
>> * assignable should not prevent assignment of raw values.  
>   
> Does it? I am not sure if this was newly introduced. I just remember having sometimes weird results with my test-cases until I finally accepted that some kind of assignment does not seem to work or be allowed.  
  
  I think I can guess what the problem is:  
using rebind_any instead of as_param for  
the arguments of the deduce_assign.  
I originally used rebind_any everywhere,  
but then added as_param, when I started  
running into overload resolution problems.  
Looks like I missed updating assignable.  
  
> I am not completely sure but I think move-constructing an `any` is not fully supported either and therefore some kind of assignments fail, too. But I could be wrong.  
>   
> Only thing I know for sure is that even with this patch type-erased move-only types (like `std::unique_ptr`) cannot be moved into `std::vector<boost::type_erasure<...>>`. Instead I have to create the vector upfront with the final number of elements and then overwrite each element through move-assignment.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: swatanabe  
> Created_at: 2017-03-25 01:53:07 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-289180147  

A couple random notes on your test case:  
  
-     copy_constructible  // Makes it ambiguous!?  
  I assume that this refers to the ambiguity with the move  
  constructor.  I actually solved this ages ago, but at the  
  time it only worked on clang, and I never updated the  
  configuration.  I've already fixed this locally, as it allows  
  move assignment support to be drastically simplified.  
-     ::boost::mpl::copy<SmartPtrConcept, ::boost::mpl::back_inserter<::boost::mpl::vector<  
  There's no need to make a copy.  Concepts can be nested arbitrarily.

---

## Comment 8

> Username: DenizThatMenace  
> Created_at: 2017-03-28 09:08:28 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-289709553  

> My intent is that assignable<T, U> and assignable<T, const U&>  
should be interchangeable.  It's actually pretty easy to do,  
if a bit weird:  
>   
> template<class T, class U>  
struct assignable : mpl::vector<assignable<T, const U&> > {};  
  
So the base template derives from a specialization of it. Clever solution.  
  
> std::forward is supposed to solve that.  
  
I know, however, at the time of writing I either forgot about that fact or - more likely - with all the weird compiler errors that occurred and your code which I did not fully understand it was probably easier to have distinct functions for rvalue- and lvalue-references.  
  
> > copy_constructible  // Makes it ambiguous!?  
>  
> I assume that this refers to the ambiguity with the move  
constructor. I actually solved this ages ago, but at the  
time it only worked on clang, and I never updated the  
configuration. I've already fixed this locally, as it allows  
move assignment support to be drastically simplified.  
  
That would be really cool if it worked with GCC, too.  
  
> > ::boost::mpl::copy<SmartPtrConcept, ::boost::mpl::back_inserter<::boost::mpl::vector<  
>  
> There's no need to make a copy. Concepts can be nested arbitrarily.  
  
I just wanted to make sure that the resulting concepts were the same as if I had them written verbatim without inheritance.  
  
  
I am really looking forward to your solution for move-assignable (and extended move-constructible) support.  
If it helps, you may send me a link to you solution before pushing it to the public develop-branch. Then I could test it with our company's code, which uses _Boost.TypeErasure_ with my move-assignable patch, and I can report any problems back to you.

---

## Comment 9

> Username: swatanabe  
> Created_at: 2017-03-28 14:50:54 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-289795240  

AMDG  
  
On 03/28/2017 03:08 AM, Deniz Bahadir wrote:  
>  
>>> copy_constructible  // Makes it ambiguous!?  
>>  
>> I assume that this refers to the ambiguity with the move  
> constructor. I actually solved this ages ago, but at the  
> time it only worked on clang, and I never updated the  
> configuration. I've already fixed this locally, as it allows  
> move assignment support to be drastically simplified.  
>   
> That would be really cool if it worked with GCC, too.  
>   
  
  Minimum compiler requirement: gcc 4.8, msvc 14.0.  
(Macro: BOOST_NO_CXX11_REF_QUALIFIERS.)  This  
support is fully general.  It works for everything,  
not just construction and assignment.  
  
>   
> I am really looking forward to your solution for move-assignable (and extended move-constructible) support.  
> If it helps, you may send me a link to you solution before pushing it to the public develop-branch. Then I could test it with our company's code, which uses _Boost.TypeErasure_ with my move-assignable patch, and I can report any problems back to you.  
>   
  
  I'm working on tests right now.  The original tests  
were vastly insufficient.  Your additions are a good  
start, but still nowhere near enough.  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: swatanabe  
> Created_at: 2017-03-30 17:12:21 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-290478318  

AMDG  
  
On 03/28/2017 03:08 AM, Deniz Bahadir wrote:  
>  
> I am really looking forward to your solution for move-assignable (and extended move-constructible) support.  
> If it helps, you may send me a link to you solution before pushing it to the public develop-branch. Then I could test it with our company's code, which uses _Boost.TypeErasure_ with my move-assignable patch, and I can report any problems back to you.  
>   
  
  I just pushed it to develop.  It should work correctly,  
but I'd appreciate it if you could try it out.  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: DenizThatMenace  
> Created_at: 2017-03-31 11:15:50 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-290686109  

I tried it out and it still works with our production-code (that up until now used my former patch).  
  
As you probably might have noticed, my personal test-cases (that I appended to one of my former comments) also still work. Additionally, you fixed the ambiguity between move- and copy-constructor.  
  
Sadly, direct assignment of raw values (test-cases 601, 602, 701 and 702) still does not work, only assigning type-erased values work. I am not sure if this is by design or an error or just my test-cases using a wrong concept.  
  
Trying to `push_back` a `type_ersure::any<move-only-type>` into a `vector` still does not work. (However, it did not work with my original patch either.)  
```  
.../boost/type_erasure/any.hpp:502:22: error: cannot pass objects of non-trivially-copyable type ‘class boost::type_erasure::any<bgp4pathattributes::AnyPathAttributeConcept>’ through ‘...’  
                 false? this->_boost_type_erasure_deduce_constructor(other) : 0  
                      ^  
.../boost/type_erasure/any.hpp:503:21: error: invalid use of void expression  
             ), other)  
                     ^  
```  
The `AnyPathAttributeConcept` looks like this:  
```  
struct AnyPathAttributeConcept : ::boost::mpl::vector<  
                                    boost::type_erasure::typeid_<>  
                                  , boost::type_erasure::relaxed  
                                  , boost::type_erasure::destructible<>  
                                  , boost::type_erasure::constructible<boost::type_erasure::_self(const boost::type_erasure::_self&, bool)>  // No normal copy-constructor!  
                                  , boost::type_erasure::constructible<boost::type_erasure::_self(boost::type_erasure::_self&&)>  
                                  , boost::type_erasure::assignable<boost::type_erasure::_self, boost::type_erasure::_self&&>  
                                  , boost::type_erasure::equality_comparable<>  
                                  , toString_concept  
                                  , ...>{};  
  
```  
I think, the `_boost_type_erasure_deduce_constructor(...)` functions need different implementations for move- and copy-constructors.  
I also had a patch which not only added "move-assignable" concept but explicit "move-constructible", too, but this did not fully work. However, it made some of these problems disappear (and new ones rise).  
If you are interested, you can still find them in my fork of this repository in branch `extended-move-support`.  
([commit d3b4159](https://github.com/Bagira80/type_erasure/commit/d3b41599ae83309a13316d0be0a79dc69d994af3))

---

## Comment 12

> Username: swatanabe  
> Created_at: 2017-03-31 14:57:19 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-290735386  

AMDG  
  
On 03/31/2017 05:15 AM, Deniz Bahadir wrote:  
>   
> Sadly, direct assignment of raw values (test-cases 601, 602, 701 and 702) still does not work, only assigning type-erased values work. I am not sure if this is by design or an error or just my test-cases using a wrong concept.  
>   
  
  It works for me with VC14.  What compiler are you using?  
I uncommented those four lines and also either relaxed  
or the assignable that takes a raw value or both.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: swatanabe  
> Created_at: 2017-03-31 15:36:00 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-290746597  

AMDG  
  
On 03/31/2017 05:15 AM, Deniz Bahadir wrote:  
>   
> Trying to `push_back` a `type_ersure::any<move-only-type>` into a `vector` still does not work. (However, it did not work with my original patch either.)  
> ```  
> .../boost/type_erasure/any.hpp:502:22: error: cannot pass objects of non-trivially-copyable type ‘class boost::type_erasure::any<bgp4pathattributes::AnyPathAttributeConcept>’ through ‘...’  
>                  false? this->_boost_type_erasure_deduce_constructor(other) : 0  
>                       ^  
> .../boost/type_erasure/any.hpp:503:21: error: invalid use of void expression  
>              ), other)  
>                      ^  
> ```  
  
  This error is a symptom of trying to construct a  
move-only any with an lvalue.  Again, it works  
for me with VC14.  I tried with gcc 4.8.3 and  
I see this error.  
  
  The source of the problem appears to be that  
the move constructor is not noexcept and the  
copy-constructor is not deleted, which results  
in vector trying to use the copy-constructor  
when reallocating.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: DenizThatMenace  
> Created_at: 2017-03-31 17:39:45 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-290779120  

I tried GCC 4.8, GCC 6 and Clang 3.8 (with libc++) on an Ubuntu 16.04 Linux.  
All have the same problems with test-cases 601, 602, 701 and 702.  
  
The test with the `vector` I only tried for GCC 4.8, but I guess it will be the same for all three compilers/versions.

---

## Comment 15

> Username: Anton3  
> Created_at: 2018-03-15 20:53:58 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-373519469  

@swatanabe What's the current state of move support? Is it merged to `develop`? How can I get it?

---

## Comment 16

> Username: swatanabe  
> Created_at: 2018-03-15 20:57:03 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-373520343  

AMDG  
  
On 03/15/2018 02:53 PM, Anton Zhilin wrote:  
> @swatanabe What's the current state of move support? Is it merged to `develop`? How can I get it?  
>   
  
It's in develop and master and will be in 1.67.  
  
In Christ,  
Steven Watanabe

---

## Comment 17

> Username: Anton3  
> Created_at: 2018-03-16 20:42:49 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-373839377  

There is still no `move_constructible` special case like `copy_constructible`. Is it intentional or just a forgotten detail?  
  
Why is movability required from the stored type for `any` to be movable? I expect the move to be like in `std::unique_ptr`, where it's as simple as copying and zeroing a pointer. In some cases the move of the underlying type is desired. But we can enable the optimization at least in `relaxed` case.  
  
With no movability requirements, we can't have both movability of `any` and usage of SBO, so if `type_erasure` uses SBO, then it will be a tricky case.

---

## Comment 18

> Username: swatanabe  
> Created_at: 2018-03-16 21:41:58 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-373852827  

AMDG  
  
On 03/16/2018 02:42 PM, Anton Zhilin wrote:  
> There is still no `move_constructible` special case like `copy_constructible`. Is it intentional or just a forgotten detail?  
>   
  
No particular reason.  It's pretty much trivial:  
template<class T = _self>  
using move_constructible = constructible<T(T&&)>;  
  
> Why is movability required from the stored type for `any` to be movable? I expect the move to be like in `std::unique_ptr`, where it's as simple as copying and zeroing a pointer. In some cases the move of the underlying type is desired. But we can enable the optimization at least in `relaxed` case.  
>   
  
  Right.  Without relaxed, Boost.TypeErasure guarantees that  
the contained object has the same lifetime as the any, and  
also forbids emptiness.  Adding the extra overload in and  
making sure that it's controlled correctly is quite complicated,  
especially for assignment and I haven't gotten around to it yet.  
  
> With no movability requirements, we can't have both movability of `any` and usage of SBO, so if `type_erasure` uses SBO, then it will be a tricky case.  
>   
  
  SBO is not currently implemented, but I don't want  
the interface to make it impossible to implement in  
the future.  In fact, this interaction between move and  
SBO is one of the main reasons why I've been putting  
off both.  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: Anton3  
> Created_at: 2018-03-17 16:26:05 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/12#issuecomment-373933057  

`Folly.Poly` solves this problem by only using SBO storage if movability is in the traits.

---
