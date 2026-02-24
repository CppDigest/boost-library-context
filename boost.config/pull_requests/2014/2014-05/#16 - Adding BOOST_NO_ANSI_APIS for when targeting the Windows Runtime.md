# #16 [winrt support] Adding BOOST_NO_ANSI_APIS for when targeting the Windows Runtime. [Closed]

> Username: stgates  
> Created at: 2014-05-23 18:36:08 UTC  
> Updated at: 2015-10-04 16:22:03 UTC  
> Closed at: 2015-10-04 16:22:03 UTC  
> Url: https://github.com/boostorg/config/pull/16  

Uses the predef added for detection of the Windows Runtime. Also added a year to the copyright for my previous change.

---

## Comment 1

> Username: Beman  
> Created_at: 2014-05-27 21:45:27 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-44339770  

Ah! I'm starting to see how this works.  
  
Couple of questions:  
- Should the include before &lt;boost/predef/platform.h&gt; since that is the only predef used?  
- Would "BOOST_NO_WINDOWS_ANSI_APIS" be a better name?  
  
I'm also a little concerned about dependencies. I'll post a query to the main list.  
  
Thanks,  
  
--Beman

---

## Comment 2

> Username: stgates  
> Created_at: 2014-05-27 21:50:49 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-44340310  

Hi Beman,  
  
According to the documentation for the Predef library to use you should just include boost/predef.h. I just followed the instructions there. It might be possible to only include boost/predef/platform.h  
  
http://www.boost.org/doc/libs/1_55_0/libs/predef/doc/html/predef/using_the_predefs.html  
  
Macro name wise this isn't a macro that I've created or added. It already existing in Boost and is in use. I figured it was better to re-use something than make another new one :)  
  
Thanks,  
Steve

---

## Comment 3

> Username: stgates  
> Created_at: 2014-06-02 18:55:04 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-44876634  

Hi Beman,  
  
Do you agree it about not renaming or creating a new macro here? I'd rather not have to go rename this in all the different repositories it is used.  
  
Is this something that will/can be accepted soon?  
  
Thanks,  
Steve

---

## Comment 4

> Username: stgates  
> Created_at: 2014-07-15 17:40:38 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-49066844  

This has been sitting for several months, is this something likely to get accepted?  
  
Thanks,  
Steve

---

## Comment 5

> Username: eldiener  
> Created_at: 2015-06-05 07:45:31 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-109192787  

The undocumented but used macro BOOST_NO_ANSI_APIS means that Windows does not have Ansi versions of the Windows API but only their corresponding wide character versions, ie does not have versions of their APIs ending in 'A' but only the equivalent version ending in 'W', such as having 'CreateFileW' but not 'CreateFileA'. It also looks like predef has no dependencies on other Boost libraries so using predef in Boost.config does not add on any additional dependencies.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-06-05 08:24:05 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-109201701  

Well yes it does, it adds predef ;)  
  
Seriously, I'd prefer not to include predef if we can get away with it, and this should be detectable from compiler defines anyway?

---

## Comment 7

> Username: mauve  
> Created_at: 2015-10-04 12:32:02 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-145345796  

Here is a similar PR but without adding a dependency on Boost.Predef: https://github.com/boostorg/config/pull/80

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2015-10-04 16:22:03 UTC  
> Url: https://github.com/boostorg/config/pull/16#issuecomment-145363886  

Merged #80 instead, closing this one.

---
