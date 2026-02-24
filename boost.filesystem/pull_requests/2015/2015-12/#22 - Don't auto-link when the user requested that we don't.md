# #22 Don't auto-link when the user requested that we don't [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 16:11:58 UTC  
> Updated at: 2018-11-25 15:16:15 UTC  
> Closed at: 2018-11-24 18:25:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/22  

Don't auto-link when the user requested that we don't

---

## Comment 1

> Username: Beman  
> Created_at: 2016-11-27 13:04:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/22#issuecomment-263121140  

I'm having trouble analyzing this. That's mostly my fault, as I wasn't even aware of the existence of BOOST_AUTO_LINK_NOMANGLE. If you could answer a couple of questions, that would help.  
  
Why are you including <boost/filesystem/config.hpp>? It's already included by <boost/filesystem/operations.hpp>  
  
Why isn't the code in <boost/filesystem/config.hpp> starting with the line "//  enable automatic library variant selection..." sufficient?  
  
What steps are needed to reproduce the problem?  
  
Thanks,  
  
--Beman

---

## Comment 2

> Username: muggenhor  
> Created_at: 2016-12-04 10:48:38 UTC  
> Updated_at: 2016-12-04 10:51:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/22#issuecomment-264696616  

@Beman:  
> Why are you including <boost/filesystem/config.hpp>? It's already included by <boost/filesystem/operations.hpp>  
  
Mostly that's the include-what-you-use mentality at work here. I.e. I'm using `BOOST_FILESYTEM_NO_LIB` directly here, so including it directly.  
  
> Why isn't the code in <boost/filesystem/config.hpp> starting with the line "// enable automatic library variant selection..." sufficient?  
  
Because this isn't at all about variant selection, this change is about giving the library user control so that she can use _not_ to link to `advapi32.lib`.  
  
> What steps are needed to reproduce the problem?  
  
I see I didn't give a problem description here, my bad. In short there's two problems, one more of a clean design one, the other a practical one.  
  
The design issue is that using auto-linking (in general) circumvents the build system, which results in hiding dependencies. This is a significant problem in the company I'm working because we're dealing with a lot of hidden/implicit dependencies and circular dependencies as a result.  
  
The practical problem is that we're targeting WinCE, and on WinCE this library (advapi32) doesn't even exist. It's functionality does, it lives in another library (I can't recall which right now). But the way the code is currently written doesn't allow me to circumvent this problem in our build system, I have to go in and do source modifications. Then I have to keep these modifications up to date with every single Boost version upgrade we wish to do.  
  
PS Just doing a rebase and noticing the addition of `#ifdef _MSC_VER`: that's not enough to prevent the WinCE problem as MSVC is still used there for building.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-11-24 18:25:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/22#issuecomment-441386495  

This is not a correct change as `BOOST_FILESYSTEM_NO_LIB` (via `BOOST_ALL_NO_LIB`) is always defined when building Boost. We want built Boost libraries to be linked to whatever they require, at the very least by default.  
  
If there is a different library to link against on WinCE then please make a separate PR that changes the library name specifically on WinCE. I have to say though that WinCE compatibility is not tested by Boost, so consider that platform virtually unsupported.

---

## Comment 4

> Username: muggenhor  
> Created_at: 2018-11-25 15:16:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/22#issuecomment-441447753  

Given that WinCE is no longer relevant for me anymore I'm going to leave this as is (i.e. not make a new PR).

---
