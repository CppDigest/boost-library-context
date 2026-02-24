# #456 Add C++11 std::array point adaptor docs [Merged]

> Username: norbertwenzel  
> Created at: 2018-01-23 22:05:08 UTC  
> Updated at: 2018-01-28 10:55:48 UTC  
> Merged at: 2018-01-28 01:23:47 UTC  
> Closed at: 2018-01-28 01:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/456  

This adds the missing documentation for the `std::array` point adaptor added in #357.   
  
Copied the documentation entry and example from Boost.Array. Referred to `std::array` as "C++11 array container" throughout the text.  
  
Link to new C++11 array container documentation page from  
* reference  
* quickref  
* point concept  
  
Generated the documentation locally and it seems everything was working fine.  
  
### Questions:  
* `std::array` needs C++11. Its example code is built successfully by the corresponding [Jamfile](https://github.com/boostorg/geometry/compare/develop...norbertwenzel:std-array-adaptor-docs?expand=1#diff-703795a7579032a7fd20c5ab34814bd0) only if compiled in C++11 mode or greater. I'm not sure if this causes any issues and/or if this can be worked around using `b2` (other than maybe explicitly adding the `-std=c++11` flag for gcc and clang).  
* Also I'm not sure if there should be an explicit warning in the docs, that C++11 array containers actually require (at least) a C++11 compiler. Are you aware of any existing doc templates that could/should be used for that?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-01-24 00:20:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/456#issuecomment-359977708  

Regarding C++11 I think what you wrote is sufficient and additional warning is not needed.  
  
Regarding the example the simplest way would be to put `#ifndef BOOST_NO_CXX11_HDR_ARRAY` around the whole code (in order to avoid displaying it in the docs) and otherwise have empty `main()`.  
  
It is possible to conditionally run a part of bjam script but I don't know if it's possible to check C++ version or available features in a consistent way across compilers.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2018-01-24 17:19:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/456#issuecomment-360207800  

@norbertwenzel  thanks a lot. I'm OK with merging - if comment of @awulkiew  is addressed.

---

## Comment 3

> Username: norbertwenzel  
> Created_at: 2018-01-25 19:26:40 UTC  
> Updated_at: 2018-01-26 06:34:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/456#issuecomment-360573151  

I'm sorry I couldn't get back any sooner.  
  
@awulkiew Thanks for your comment. I've done as advised.  
  
@barendgehrels I've looked at the initial PR #357 and you've asked me there to update the copyright in source files despite my trivial changes. Is this necessary here too?  
  
And to both: do you want me to squash all my changes into a single commit, since it's dealing with a single, minor feature only or do you not mind multiple commits.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-01-26 23:32:08 UTC  
> Updated_at: 2018-01-26 23:33:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/456#issuecomment-360934081  

Thanks!  
  
I do not mind multiple commits. I'm ok with merging the PR as it is.  
  
Regarding the copyright. It's your contribution. I see this PR as a complementary part of the `std::array` adaptation and you added copyright there. I think it's big enough to get credit for it. You could also add your name with e.g. *"(adaption of std::array)"* to the main docs page at the bottom under **Contributions** (see: http://www.boost.org/libs/geometry).  
  
If you decided to squash them into one commit please prefix commit message with `[doc]`. At Boost.Geometry we have a convention of writing commit messages containing prefixes indicating parts of the library that were changed.

---

## Comment 5

> Username: norbertwenzel  
> Created_at: 2018-01-27 10:03:28 UTC  
> Updated_at: 2018-01-27 10:03:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/456#issuecomment-360974396  

@awulkiew I've now squashed the commit and added the `[doc]` prefix. I do not want to claim any copyright since this PR is only copied from existing code in your repository. I was just under the impression that the copyright was a requirement for merging the last time.  
  
I consider the PR now finished, so if you agree please merge. Thanks.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2018-01-28 01:23:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/456#issuecomment-361030261  

Ok, thanks!

---
