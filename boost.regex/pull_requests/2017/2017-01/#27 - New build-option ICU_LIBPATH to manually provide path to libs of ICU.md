# #27 New build-option ICU_LIBPATH to manually provide path to libs of ICU. [Closed]

> Username: DenizThatMenace  
> Created at: 2017-01-05 10:54:44 UTC  
> Updated at: 2017-07-13 14:39:32 UTC  
> Closed at: 2017-07-13 14:39:32 UTC  
> Url: https://github.com/boostorg/regex/pull/27  

When using _Boost.Build_ to build _**Boost.Regex**_ one can build and link with the external library **ICU** and provide the path to its (root-)directory using the option `ICU_PATH`. The ICU library files are searched in a sub-directory `lib` (or in some situations in `lib64`) of _`<ICU_PATH>`_.  
However, that only works if the library files are directly located in `<ICU_PATH>/lib` (or `<ICU_PATH>/lib64`).  
  
If they are installed in a custom location,  
* either a totally different location,  
* another named sub-directory (e.g. `<ICU_PATH>/libraries` ) or  
* even in a sub-directory of the expected sub-directory (e.g. `<ICU_PATH>/lib/x86_64-linux-gnu`),  
  
this does no longer work and the ICU libs cannot be found.  
  
Therefore, this pull-request provides an additional _Boost.Build_ option `ICU_LIBPATH` with which one can provide the path to the directory where the **ICU** libraries are located.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-01-05 13:16:48 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-270641651  

While I understand the issue, how is this better than the already documented linkflags= option, ie just using:  
  
bjam linkflags=-Lpath-to-binaries

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2017-01-05 14:13:24 UTC  
> Updated_at: 2017-01-06 09:34:35 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-270652759  

I did not know about the `linkflags=` option.  
  
However, I see a possible problem with the `linkflags=-Lpath-to-binaries` approach:  
When building all boost-libraries with `bjam`/`b2` and using that option it (probably) will be applied to each individual boost-library, which might not be what the user wants.  
  
Particularly, if that path contains some other libraries to which another boost-library might link that the user does not want to link to (or for which the user wants to provide another directory where the libraries are located that shall be linked).  
  
With my proposed ICU_LIBPATH option this would not be a problem because the Linker-search-path will only be provided where it is needed.  
  
However, I am not insisting on this change. If you do not like it I can retreat and close it.  
(The similar [pull-request](https://github.com/boostorg/locale/pull/13) for _Boost.Locale_ should be treated the same.)

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-01-06 18:10:19 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-270965765  

On 05/01/2017 14:13, Deniz Bahadir wrote:  
>  
> Probably, because I did not know about the |linkflags=| option. ;-)  
>  
> However, I see a possible problem with the   
> |linkflags=-Lpath-to-binaries| approach:  
> When building all boost-libraries with |bjam|/|b2| and using that   
> option it (probably) will be applied to each individual boost-library,   
> which might not be what the user wants.  
>  
> Particularly, if that path contains some other libraries to which   
> another boost-library might link that the user does not want to link   
> to (or for which the user wants to provide another directory where the   
> libraries are located that shall be linked).  
>  
  
There's also ICU_LINK which can be used for this purpose, as in:  
  
export ICU_LINK="-Lpath-to-libraries -licuuc -licudt -licuin"

---

## Comment 4

> Username: Amua  
> Created_at: 2017-01-07 13:33:14 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-271084069  

I meet a problem, if the rule is chinese the code boost::make_u32regex("中文测试") will not work  
  
  
// u32regex.cpp : Defines the entry point for the console application.  
//  
  
#include "stdafx.h"  
  
#define BOOST_HAS_ICU  
  
#include <iostream>  
#include <boost/regex.hpp>  
#include <boost/regex/icu.hpp>  
  
#pragma comment(lib, "icudt.lib")  
#pragma comment(lib, "icuin.lib")  
#pragma comment(lib, "icuuc.lib")  
  
int _tmain(int argc, _TCHAR* argv[])   
{  
	using namespace std;	  
	  
	boost::u32regex re = boost::make_u32regex("这是一个测试");  // Chinese rules will not work, English rules is good.  
	cout << boost::u32regex_search("这是一个测试", re) << endl; //   
    
	return 0;   
}

---

## Comment 5

> Username: Amua  
> Created_at: 2017-01-07 13:37:59 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-271084327  

My environment is VS2010     boost_1_55_0    icu4c-49_1_2

---

## Comment 6

> Username: DenizThatMenace  
> Created_at: 2017-01-09 15:30:25 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-271313719  

Hi @Amua , I am sorry but your problem seems to be unrelated to this pull-request, is it not?  
  
You should probably create a separate issue to *Boost.Regex* at Boost's [issue-tracking system](https://svn.boost.org/trac/boost) or write an email to the [Boost-Users mailing-list](http://www.boost.org/community/groups.html#users).

---

## Comment 7

> Username: DenizThatMenace  
> Created_at: 2017-01-10 10:38:53 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-271542297  

Hi @jzmaddock   
  
> export ICU_LINK="-Lpath-to-libraries -licuuc -licudt -licuin"  
  
When building with this option _Boost.Build_ seems to find _ICU_ because it says:  
`    - icu                      : yes`  
However, it does not find it properly because it also says:  
`    - has_icu builds           : no`  
Then again, it seems to compile fine. However, probably without _ICU_. At least the resulting shared libraries are not linked to _ICU_ shared libraries.  
  
Do you known, what is going on?  
(When using the `ICU_LIBPATH` option both messages say `yes` and the resulting shared libraries are linked to _ICU_ shared libraries.)

---

## Comment 8

> Username: Amua  
> Created_at: 2017-01-10 10:55:43 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-271545940  

Hi @Bagira80   
  
I know the reason,  the first args of  boost::make_u32regex() must be utf-8 code.  
for example:  
     boost::make_u32regex(AsciiToUtf8("这是一个测试"))   // the  code is good.

---

## Comment 9

> Username: Amua  
> Created_at: 2017-01-10 10:57:05 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-271546224  

Hi @Bagira80   
  
I know the reason,  the first args of  boost::make_u32regex() must be utf-8 code.  
for example:  
     boost::make_u32regex(AsciiToUtf8("这是一个测试"))   // the  code is good.

---

## Comment 10

> Username: DenizThatMenace  
> Created_at: 2017-07-13 14:39:32 UTC  
> Url: https://github.com/boostorg/regex/pull/27#issuecomment-315097817  

Closing this pull-request, as the associated [pull-request to *Boost.Locale*](https://github.com/boostorg/locale/pull/13) was closed without merge, too.  
  
`LINK_ICU` probably/hopefully works (despite the irritating messages of *Boost.Build*.

---
