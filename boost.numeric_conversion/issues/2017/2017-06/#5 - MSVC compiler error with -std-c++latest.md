# #5 - MSVC compiler error with /std:c++latest [Closed]

> Username: ysangkok  
> Created at: 2017-06-14 12:56:29 UTC  
> Updated at: 2018-10-03 07:36:50 UTC  
> Closed at: 2017-08-22 18:12:58 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5  

Using the newest Visual Studio 2017 and its C++ compiler:  
  
The errors mean   
* ',' is missing before ''  
*  *Siehe Verweis auf die Klasse Vorlage-  Instanziierung*:  See reference to the Class Template-Instantiation  
* *Schlüsselwort "typename" ist in* : Keyword 'typename' is in  
  
Could this be a compiler error?  
  
```  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(453): error C2143: Syntaxfehler: Es fehlt "," vor "  
<" [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
  C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(467): note: Siehe Verweis auf die Klasse Vorlage-  
  Instanziierung "boost::numeric::convdetail::trivial_converter_impl<Traits>", die kompiliert wird.  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(453): error C2518: Schlüsselwort "typename" ist in  
der Basisklassenliste ungültig und wird ignoriert [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(454): error C2518: Schlüsselwort "typename" ist in  
der Basisklassenliste ungültig und wird ignoriert [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(474): error C2143: Syntaxfehler: Es fehlt "," vor "  
<" [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
  C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(497): note: Siehe Verweis auf die Klasse Vorlage-  
  Instanziierung "boost::numeric::convdetail::rounding_converter<Traits,RangeChecker,RawConverter,Float2IntRounder>", d  
  ie kompiliert wird.  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(474): error C2518: Schlüsselwort "typename" ist in  
der Basisklassenliste ungültig und wird ignoriert [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(475): error C2518: Schlüsselwort "typename" ist in  
der Basisklassenliste ungültig und wird ignoriert [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(504): error C2143: Syntaxfehler: Es fehlt "," vor "  
<" [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
  C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(526): note: Siehe Verweis auf die Klasse Vorlage-  
  Instanziierung "boost::numeric::convdetail::non_rounding_converter<Traits,RangeChecker,RawConverter>", die kompiliert  
   wird.  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(504): error C2518: Schlüsselwort "typename" ist in  
der Basisklassenliste ungültig und wird ignoriert [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
C:\local\boost_1_64_0\boost/numeric/conversion/detail/converter.hpp(505): error C2518: Schlüsselwort "typename" ist in  
der Basisklassenliste ungültig und wird ignoriert [C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
C:\local\boost_1_64_0\boost/algorithm/string/detail/case_conv.hpp(33): error C2143: Syntaxfehler: Es fehlt "," vor "<"  
[C:\Users\jtroelsen\ifcconverter\ifcconverter.vcxproj]  
```  
  
https://github.com/boostorg/numeric_conversion/blob/boost-1.64.0/include/boost/numeric/conversion/detail/converter.hpp#L453

---

## Comment 1

> Username: PatrickMGrace  
> Created at: 2017-07-01 12:18:31 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-312429094  

I am getting what seems to be the same error:  
...\boost\numeric\conversion\detail\converter.hpp(453): error C2143: syntax error: missing ',' before '<'

---

## Comment 2

> Username: BurningEnlightenment  
> Created at: 2017-08-03 12:32:28 UTC  
> Updated at: 2017-08-03 12:53:17 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-319955620  

No, this isn't a compiler error. If you look at the code, you will notice that the errors stem from the usage of `std::unary_function` which was removed in c++17 and MSVC doesn't provide the class anymore when compiling with `/std:c++latest`.  
This was fixed with 593aafab5a7aba86f14df5f9a14a53fc4353bb00 and for an unknown reason reverted with 2009f0d4fc49a6db59b950a3140f8d1f58e1a8c6.  
@brandon-kohn can you provide some insight, why it was reverted and when we can expect c++17 compliance?  
  
[EDIT] see: http://boost.2283326.n4.nabble.com/Boost-numeric-conversion-unary-function-deprecated-or-removed-for-C-17-td4687293.html

---

## Comment 3

> Username: brandon-kohn  
> Created at: 2017-08-22 18:12:58 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-324108355  

Fix is up in master now. Please re-open if there is some lingering issue.

---

## Comment 4

> Username: DoumanAsh  
> Created at: 2017-09-12 11:44:58 UTC  
> Updated at: 2017-09-12 11:48:28 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-328827887  

Is it included into current release (1.65.1)?  
p.s. asking cuz i'm seeing it currently.  
  
**UPD:**  
It is not present yet,  but i downloaded headers from master branch and it compiles without an issue.

---

## Comment 5

> Username: PBonnema  
> Created at: 2017-09-17 02:04:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-330007241  

@DoumanAsh I have this problem to but I don't see the actual header files on the repo. Am I missing something? Same for the development branch.

---

## Comment 6

> Username: DoumanAsh  
> Created at: 2017-09-17 02:54:51 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-330009831  

What do you mean not seeing actual header files?  
They lies by `include/boost`

---

## Comment 7

> Username: PBonnema  
> Created at: 2017-09-17 10:23:23 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-330034324  

I see I'm mistaken. I'm new to boost and I assumed that the boost project contained all libraries but it doesn't. You have to clone all libs you want separately. Mystery solved.

---

## Comment 8

> Username: rathaROG  
> Created at: 2017-11-04 11:18:11 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-341889162  

I'm new to boost and C++ and I have this problem too. So what should I do? Download and replace? Or I have to build something?

---

## Comment 9

> Username: BurningEnlightenment  
> Created at: 2017-11-04 19:11:57 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-341922480  

FYI @rathaROG the possible workarounds are summed up [here](https://www.reddit.com/r/cpp/comments/67vesx/has_the_boost_library_issue_with_visual_studio/dgtjytj/)

---

## Comment 10

> Username: rathaROG  
> Created at: 2017-11-04 19:52:02 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-341925142  

Thank you so much @BurningEnlightenment !

---

## Comment 11

> Username: MichaelVoelkel  
> Created at: 2018-06-05 09:53:27 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-394651170  

In Boost 1_67_0, the error exists (again!?)

---

## Comment 12

> Username: brandon-kohn  
> Created at: 2018-07-05 18:22:00 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-402810942  

I don't have any issues compiling on msvc-14.1. Could one of you post a small example reproducing your issue?

---

## Comment 13

> Username: travnick  
> Created at: 2018-10-03 07:36:41 UTC  
> Updated at: 2018-10-03 07:36:50 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/5#issuecomment-426540255  

I guess by `/std:c++latest` they mean `/std:c++17` which is available since `MSVC++ 14.11` (`Visual Studio 2017 version 15.3`)
