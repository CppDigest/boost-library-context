# #132 - undefined reference to `boost::locale::ios_info::get(std::ios_base&)' [Closed]

> Username: nlogozzo  
> Created at: 2022-11-13 01:58:34 UTC  
> Updated at: 2022-11-13 15:03:14 UTC  
> Closed at: 2022-11-13 15:03:14 UTC  
> Url: https://github.com/boostorg/locale/issues/132  

I'm having an issue getting my code to compile using boost::locale. I have the library built using flatpak:  
```json  
{  
	    "name": "boost",  
	    "buildsystem": "simple",  
	    "build-commands": [  
		"./bootstrap.sh --prefix=/app --with-libraries='system multiprecision locale'",  
		"./b2 -j $FLATPAK_BUILDER_N_JOBS install"  
	    ],  
	    "sources": [  
		{  
		    "type": "archive",  
		    "url": "https://boostorg.jfrog.io/artifactory/main/release/1.80.0/source/boost_1_80_0.tar.bz2",  
		    "sha256": "1e19565d82e43bc59209a168f5ac899d3ba471d55c7610c677d4ccf2c9c500c0"  
		}  
	    ]  
},  
```  
and am using the library as such:  
```cpp  
#include "moneyhelpers.hpp"  
#include <sstream>  
#include <boost/locale.hpp>  
  
std::string MoneyHelpers::boostMoneyToLocaleString(boost::multiprecision::cpp_dec_float_50 amount, const std::locale& locale)  
{  
    std::stringstream builder;  
    builder.imbue(locale);  
    builder << boost::locale::as::currency << amount;  
    return builder.str();  
}  
```  
However, when I try to build and run the application, I'm getting this `undefined reference` error:  
```  
moneyhelpers.cpp:(.text+0x23d): undefined reference to `boost::locale::ios_info::get(std::ios_base&)'  
/usr/lib/gcc/x86_64-unknown-linux-gnu/12.1.0/../../../../x86_64-unknown-linux-gnu/bin/ld: moneyhelpers.cpp:(.text+0x24a): undefined reference to `boost::locale::ios_info::display_flags(unsigned long)'  
```  
Any help would be greatly appreciated as I cannot find any information on `boost::locale::ios_info`

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-11-13 10:58:56 UTC  
> Url: https://github.com/boostorg/locale/issues/132#issuecomment-1312702022  

It looks like your are not linking to Boost.Locale. Check the link command to verify.  
Also `"./bootstrap.sh --prefix=/app --with-libraries='system multiprecision locale'",` has a mistake which may explain this:  
  
`--with-libraries` should be a comma-separated list, i.e. `--with-libraries=system,multiprecision,locale` and as `multiprecision` is header-only this will error so you have to remove it yielding `"./bootstrap.sh --prefix=/app --with-libraries=system,locale",`  
  
Let me know if that solves your issue.

---

## Comment 2

> Username: nlogozzo  
> Created at: 2022-11-13 15:03:14 UTC  
> Url: https://github.com/boostorg/locale/issues/132#issuecomment-1312751388  

That fixed it! Thank you so much!!
