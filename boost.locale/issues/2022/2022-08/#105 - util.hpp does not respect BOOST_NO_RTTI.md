# #105 - util.hpp does not respect BOOST_NO_RTTI [Closed]

> Username: masodikbela  
> Created at: 2022-08-20 11:53:08 UTC  
> Updated at: 2022-08-24 15:04:32 UTC  
> Closed at: 2022-08-20 13:34:10 UTC  
> Url: https://github.com/boostorg/locale/issues/105  

The typeid usage in the following line is not protected by the BOOST_NO_RTTI macro, and therefore generates a warning if the compiler has run-time type info disabled, even if the BOOST_NO_RTTI is defined correctly in boost/config/user.hpp.  
https://github.com/boostorg/locale/blob/76f6226fb12c900d0cc5c5744c30dc36a36d5b9f/include/boost/locale/util.hpp#L122  
  
Minimal program to reproduce: (using non-icu backend, not sure if that has an impact on this or not)  
```cpp  
#include <boost/locale.hpp>  
  
int main()  
{  
	auto result = boost::locale::conv::utf_to_utf<char>("input");  
	return 0;  
}  
```  
  
The warning message using the latest msvc compiler:  
```  
Warning	C4541	'typeid' used on polymorphic type 'boost::locale::util::base_converter' with /GR-; unpredictable behavior may result	include\boost\locale\util.hpp	122	  
```

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-08-20 12:08:49 UTC  
> Url: https://github.com/boostorg/locale/issues/105#issuecomment-1221303167  

Boost.Locale does not worth without RTTI

---

## Comment 2

> Username: masodikbela  
> Created at: 2022-08-20 12:51:50 UTC  
> Url: https://github.com/boostorg/locale/issues/105#issuecomment-1221309487  

I see, thats good to know. I haven't seen any notice about the relation of Boost.Locale and RTTI anywhere in the documentations, and as an ordinary user of the library I have little-to-no knowledge of its internals. In case if it wasn't just me missing it, it would be helpful to have such a notification somewhere in the docs.  
  
In my case I cannot use RTTI in my project, and I only needed a simple between-utf conversion, but since c++17 the standard codecvt way marked as "deprecated" (even tho its in a weird state since its marked as deprecated but there is no alternative for this in the standard library as far as I know) and decided since I already use parts of boost I could just use this from locale, and wouldn't have to look for other libraries.

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2022-08-20 13:34:07 UTC  
> Url: https://github.com/boostorg/locale/issues/105#issuecomment-1221316173  

Generally speaking RTTI is a fundamental part of C++. Without it you can't have working exceptions, without exceptions even basic things become broken in C++ and it isn't C++ any more.  
  
For example if you create `std::locale` with invalid locale the error is reported via exceptions.

---

## Comment 4

> Username: masodikbela  
> Created at: 2022-08-20 14:34:46 UTC  
> Updated at: 2022-08-20 14:38:47 UTC  
> Url: https://github.com/boostorg/locale/issues/105#issuecomment-1221325682  

As far as I know exceptions aren't necessary related to RTTI directly. There might be some cases where some parts of the standard c++ exceptions rendered unusable with RTTI disabled, but generally speaking its not the case, and I haven't encountered such cases where that would happen. There might be some platforms where exceptions are RTTI dependent since they are implemented using RTTI (even tho I couldn't name any out of the blue right now), but in general that is not the case.  
  
Generally speaking RTTI is only related to typeid and dynamic_cast operators and the type_info class which holds the info returned by typeid. These seems useful indeed for implementing exceptions, but its not required.  
  
Also according to the [Boost.Exception library](https://www.boost.org/doc/libs/1_80_0/libs/exception/doc/configuration_macros.html) its stated that boost has an internal RTTI system in such cases when the user disables the language RTTI system. Whether or not worth to use it performance wise should be decided by the user of the library.  
  
So all in all I think the library should respect the BOOST_NO_RTTI macro even if it reduces usability or degrades performance, since the use of typeid or dynamic_cast in the absence of RTTI is actually dangerous and an undefined behaviour.

---

## Comment 5

> Username: Flamefire  
> Created at: 2022-08-22 16:33:17 UTC  
> Url: https://github.com/boostorg/locale/issues/105#issuecomment-1222608821  

> Also according to the [Boost.Exception library](https://www.boost.org/doc/libs/1_80_0/libs/exception/doc/configuration_macros.html) its stated that boost has an internal RTTI system in such cases when the user disables the language RTTI system. Whether or not worth to use it performance wise should be decided by the user of the library.  
  
Sorry but that states that Boost **.Exception** has an internal RTTI system, not Boost in general.  
  
> So all in all I think the library should respect the BOOST_NO_RTTI macro even if it reduces usability or degrades performance, since the use of typeid or dynamic_cast in the absence of RTTI is actually dangerous and an undefined behaviour.  
  
This library (internally) uses both typeid and dynamic_cast, so RTTI is required.  
  
There currently are discussions to provide UTF-conversions in the header-only Boost.Core lib. I guess that would be something for your use-case?

---

## Comment 6

> Username: masodikbela  
> Created at: 2022-08-24 14:51:04 UTC  
> Updated at: 2022-08-24 15:04:32 UTC  
> Url: https://github.com/boostorg/locale/issues/105#issuecomment-1225834081  

> Sorry but that states that Boost .Exception has an internal RTTI system, not Boost in general.  
  
Oh I see, in that case I'm sorry it was me misunderstanding it.  
  
> This library (internally) uses both typeid and dynamic_cast, so RTTI is required.  
  
In that case it makes sense indeed, it was just me misinterpreting that Boost.Exception's BOOST_NO_RTTI macro is a "global" definition and other parts of boost makes use of this.  
  
> There currently are discussions to provide UTF-conversions in the header-only Boost.Core lib. I guess that would be something for your use-case?  
  
Yes indeed that is what I was looking for, because the std way is marked as "deprecated", and saw that Boost.Locale has this.
