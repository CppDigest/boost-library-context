# #240 - boost-1.68.0: error: ignoring #pragma detect_mismatch [Closed]

> Username: 0-wiz-0  
> Created at: 2018-08-18 04:37:46 UTC  
> Updated at: 2018-12-02 20:46:58 UTC  
> Closed at: 2018-12-02 20:46:58 UTC  
> Url: https://github.com/boostorg/config/issues/240  

On NetBSD 8.99.24, gcc (nb2 20180327) 6.4.0, with boost from pkgsrc, gnucash 3.2 fails with boost-1.68.0 but did not fail with boost-1.67.0.  
The gnucash error is:  
````  
In file included from /usr/pkg/include/boost/variant/variant.hpp:21:0,  
                 from /usr/pkg/include/boost/variant.hpp:17,  
                 from .../gnucash-3.2/libgnucash/engine/kvp-value.hpp:36,  
                 from .../gnucash-3.2/libgnucash/engine/kvp-frame.hpp:87,  
                 from .../gnucash-3.2/libgnucash/engine/test-core/test-engine-stuff.cpp:39:  
/usr/pkg/include/boost/type_index.hpp:37:0: error: ignoring #pragma detect_mismatch  [-Werror=unknown-pragmas]  
 #           pragma detect_mismatch( "boost__type_index__abi", "RTTI is used")  
   
````  
When building boost, I see:  
````  
boost/config/user.hpp:#define BOOST_HAS_PRAGMA_DETECT_MISMATCH  
````  
the symbol is defined, but it seems the compiler does not like it anyway.   
I don't understand the build system well enough to see where/why this is defined, but something's going wrong.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-10-06 09:00:37 UTC  
> Url: https://github.com/boostorg/config/issues/240#issuecomment-427558264  

boost/config/user.hpp is for user-specific defines, ie stuff that *we* don't set.  In particular we do not set BOOST_HAS_PRAGMA_DETECT_MISMATCH for gcc as it's unsupported by that compiler.  Has someone edited your user.hpp?

---

## Comment 2

> Username: 0-wiz-0  
> Created at: 2018-10-06 13:49:03 UTC  
> Url: https://github.com/boostorg/config/issues/240#issuecomment-427575294  

No, the file is still like it was installed using pkgsrc.  
The pkgsrc patches for boost are available here:  
http://cvsweb.netbsd.org/bsdweb.cgi/pkgsrc/meta-pkgs/boost/patches/?only_with_tag=MAIN  
  
What do you mean with "_we_ don't set it" - how is the file generated? Where can I look for the code that might add this symbol?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-10-06 17:16:12 UTC  
> Url: https://github.com/boostorg/config/issues/240#issuecomment-427591025  

OK found it - they're running the configure script and taking the results as correct even though it's really only a first guess - there are some things that are quite hard to test for.  Let me double check the script.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-10-06 17:30:00 UTC  
> Url: https://github.com/boostorg/config/issues/240#issuecomment-427592130  

Here's what's happening: when the configure script is run, then `-Werror=unknown-pragmas` is *not* set and the test case compiles (albeit with a warning) so the script thinks that the pragma is supported.  Then when you compile your code with -Werror or whatever then it crashes and burns.  
  
I could manually tweak the test case so it fails for any unsupported compilers, but that sort of defeats the object really.  For now I suggest you just comment out the line the config/user.hpp that defines `BOOST_HAS_PRAGMA_DETECT_MISMATCH`  
  
BTW the configure script does print out right at the start:  
  
```  
Note that this test script only gives an approximate  
configuration - you will need to test the results carefully  
using the boost regression test suite before using the results.  
```  
  
It was really designed to give a first approximation when porting Boost to a new platform, usually relying on the pre-configured headers will give better results.

---

## Comment 5

> Username: 0-wiz-0  
> Created at: 2018-12-02 20:46:58 UTC  
> Url: https://github.com/boostorg/config/issues/240#issuecomment-443540581  

I changed pkgsrc to use the pre-generated `user.hpp` file instead.  
Thank you for your helpful advice!
