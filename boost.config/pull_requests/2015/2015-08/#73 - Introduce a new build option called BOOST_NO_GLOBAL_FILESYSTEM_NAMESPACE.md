# #73 Introduce a new build option called BOOST_NO_GLOBAL_FILESYSTEM_NAMESPACE. [Closed]

> Username: EdSchouten  
> Created at: 2015-08-30 09:28:27 UTC  
> Updated at: 2018-08-06 11:29:50 UTC  
> Closed at: 2018-08-06 11:29:50 UTC  
> Url: https://github.com/boostorg/config/pull/73  

Systems like FreeBSD Capsicum and Nuxi CloudABI provide purely  
capability-based runtime environments. In these environments it is  
possible to access the filesystems only through handles to directories  
as part of their security model. There is no global filesystem  
namespace.  
  
Most of Boost seems to build pretty well in these environments, with the  
exception of classes that operate on file contents. Many of these  
classes allow you to pass in file streams, but also provide utility  
functions that open these files on your behalf. These will need to be  
disabled in this environment.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-09-07 16:58:43 UTC  
> Url: https://github.com/boostorg/config/pull/73#issuecomment-138340487  

Thanks for the patch.  
  
Under what circumstances should this new macro be defined?  How would we detect these platforms?

---

## Comment 2

> Username: Beman  
> Created_at: 2015-09-07 22:16:00 UTC  
> Url: https://github.com/boostorg/config/pull/73#issuecomment-138384744  

Providing only a new config macro without the rest of the package strikes me as a mistake.  
  
What would be more helpful is a proposal specifying the environment or environments and then suggesting specific modifications to specific Boost libraries. And of course nothing will happen until someone contributes a test runner for whatever environments we are being asked to support.  
  
The Filesystem working group of the C++ standards committee has gotten similar arm-waving requests WRT these kinds of filesystem. But there is no plan to do anything until someone produces a written proposal for a complete solution, not just a request that we do a bunch of work that those doing the asking are unwilling or unable to do themselves. AFAICS, the same applies to Boost.  
  
I'm very interested in high-security filesystems, but some kind of dialog needs to be established long before we start adding macros to Boost.Config.  
  
Thanks,  
  
--Beman

---

## Comment 3

> Username: EdSchouten  
> Created_at: 2015-09-10 15:08:30 UTC  
> Url: https://github.com/boostorg/config/pull/73#issuecomment-139274728  

Hi John,  
  
Let me first answer your question:  
  
This option needs to be defined in `cloudabi.hpp`. This file was only added after I sent out this pull request (thanks for that), so I've just updated the diff to add it to `cloudabi.hpp`.  
  
I think the best indicator for the absence of global filesystem namespace access is the absence of the `fopen()` function. People could still use `fdopen()` to turn file descriptors into stdio objects, but they cannot acquire them by only providing a pathname.  
  
The test that I added is almost identical to the existing test for `<dirent.h>, but tests against`fopen()`instead of`opendir()`.  
  
Beman,  
  
First of all, let me clarify that the intent of this change is only to disable features that are currently present in Boost that depend on the presence of the global filesystem namespace. No functionality will be added or extended by this build option. A couple of examples:  
  
https://github.com/boostorg/program_options/blob/develop/include/boost/program_options/parsers.hpp#L188-L199  
https://github.com/boostorg/program_options/blob/develop/src/parsers.cpp#L143-L156  
  
This class can read its configuration from an `istream`, but offers an extra utility function to open the file on your behalf. This is where this build option could come in, to disable this utility function. Another example:  
  
https://github.com/boostorg/locale/blob/master/src/shared/message.cpp#L43-L102  
https://github.com/boostorg/locale/blob/master/src/shared/message.cpp#L630-L642  
  
This function can be used to parse GNU Gettext `*.mo` files. It can either open the files from disk or use a callback. When no global filesystem namespace is available, we'd like to disable the former. You could only use a callback in that case.  
  
I am indeed aware of the discussions that have taken place within the committee. The lack of a standardized filesystem API that addresses this is annoying, but my opinion would be that conditionally disabling features that depend on the presence of a global filesystem namespace to at least have Boost build on platforms that don't provide this concept is orthogonal to that discussion.  
  
I am currently in the process of porting Boost to CloudABI. Once I manage to get this to build and have it start the test suite, I am more than happy to set up a test runner.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-08-06 11:29:50 UTC  
> Url: https://github.com/boostorg/config/pull/73#issuecomment-410677759  

I think I'm going to close this - when individual libraries need the macro they can lobby for it - I think I would prefer a better name as well as the "namespace" part sends the wrong signals to me at least.

---
