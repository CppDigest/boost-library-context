# #18 Header file cleanup for C++20 header-units [Closed]

> Username: urnathan  
> Created at: 2022-05-16 15:12:50 UTC  
> Updated at: 2022-05-17 18:14:15 UTC  
> Closed at: 2022-05-17 18:14:15 UTC  
> Url: https://github.com/boostorg/align/pull/18  

C++20 adds 'header units' as a stepping-stone to modules.  Header  
units are regular header-files that have a 'self-contained' property  
-- they do not require previously-included headers to provide typedefs  
and what not.  
  
This addresses a problem discovered when using clang modules (as a  
proxy for C++20 header-units).  
  
Some headers do not #include all the headers they need, relying on  
their includer already having done that.  That breaks the above  
mentioned encapuslation requirement.  Fixed by including the missing  
headers.

---

## Comment 1

> Username: urnathan  
> Created_at: 2022-05-16 15:13:40 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1127800335  

FYI, I have another 7 such patches, across the various boost subprojects.  Let's get the workflow sorted first though :)

---

## Comment 2

> Username: glenfe  
> Created_at: 2022-05-17 14:05:37 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1128915012  

Also, just so that I understand completely, you're saying it doesn't matter that `<boost/align/detail/is_alignment.hpp>` included by this header already includes `<cstddef>`. i.e. This header needs to include it too.  Yes?

---

## Comment 3

> Username: urnathan  
> Created_at: 2022-05-17 15:11:29 UTC  
> Updated_at: 2022-05-17 15:13:14 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1128993170  

I don't know if &lt;cstddef&gt; needs including or not.  All I know is that is_alignment.hpp provided a declaration that is needed. (I can't remember what specific declaration it was. sorry)

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-05-17 15:31:04 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1129015687  

There are two things I don't understand here. First, `<cstddef>` is included by `<boost/align/detail/is_alignment.hpp>`, here: https://github.com/boostorg/align/blob/bef21416a3bbd46f3f5de4f9254bf4c46ec752fe/include/boost/align/detail/is_alignment.hpp#L12  
  
So I don't see why it needs to be included again. Is that a header unit requirement?  
  
Second, this is a detail header, which means that users are not allowed to include or import it directly. This means that it doesn't need to be self-contained, right? (Unless include to import automatic translation is on, but MSVC actually has no problem with it even with that option on.)  
  
(If GCC header units impose a requirement that detail headers need to be self-contained, Boost.System will never be usable as a GCC header unit. It's not possible to make its detail headers self-contained because the definitions of error_category, error_condition and error_code are circular.)

---

## Comment 5

> Username: urnathan  
> Created_at: 2022-05-17 15:55:46 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1129043274  

<quote>(If GCC header units impose a requirement that detail headers need to be self-contained, Boost.System will never be usable as a GCC header unit. It's not possible to make its detail headers self-contained because the definitions of error_category, error_condition and error_code are circular.)</quote>  
  
It's not a GCC requirement, it's a C++20 header-unit requirement.  Let me briefly explain.  
  
C++20's module scheme has these things called 'header-units', which are built from 'header-files'.  Think precompiled-headers.  But unlike many (most? all?) PCM implementations, the compiler can read in multiple header-units. One builds a header-unit's on-disk representation by compiling the associated header-file in some manner.  That compilation, just like a regular source compilation, needs to see all the declarations it references.  That's the problem here -- is_alignment.hpp referred to an entity that was not declared by the (sub)include graph within is_alignment.hpp itself.  
  
Header-units can be imported either explicitly by 'import "header-name";' declarations, or via a mechanism called include-translation, where '#include "header-name"' is converted into the former declaration.  Only importable header-files may be so include-translated.  Unimportable ones remain as the traditional (textual) include.  
  
Why would one desire include-translation?  It means the compiler only needs to parse the header file (at most) once per build, rather than once per compilation.  Further, multiple textual inclusions within a header-unit-using compiulation result in multiple declarations appearing with in the import graph, and the compiler ends up doing more work to merge those declarations (essentially proving they are all the same).  
  
So it is desirable to have as many headers as possible be importable header-units.  But it is not a show-stopper if (a subset of) headers cannot be made so (like the circular set you mention).  
  
Hope that helps.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-05-17 16:08:14 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1129056338  

If you're talking about https://github.com/boostorg/align/blob/develop/include/boost/align/detail/is_alignment.hpp, it does include `<cstddef>` (needed for `std::size_t`).

---

## Comment 7

> Username: glenfe  
> Created_at: 2022-05-17 16:29:46 UTC  
> Url: https://github.com/boostorg/align/pull/18#issuecomment-1129078222  

@urnathan I want to make sure we're on the same page:  
  
**1.**  
  
You made a PR to a single header `<boost/align/detail/align_down.hpp>` to add an include for `<cstddef>`.  
  
But  `<boost/align/detail/align_down.hpp>`  already includes `<cstddef>`.  
  
Because  `<boost/align/detail/align_down.hpp>`  includes `<boost/align/detail/is_alignment.hpp>`  which includes `<cstddef>`.  
  
For more than 30 years, if `<A>` includes `<B>`, and `<B>` includes `<C>`, then `<A>` does not need to include `<C>` again.   But now, with header units, we're saying this has changed? i.e. Now `<A>` has to include `<C>` too?  
  
**2.**  
  
Why is  `<boost/align/detail/align_down.hpp>` special? i.e. Why does your PR not do the same for ` <boost/align/detail/align_up.hpp>`?

---
