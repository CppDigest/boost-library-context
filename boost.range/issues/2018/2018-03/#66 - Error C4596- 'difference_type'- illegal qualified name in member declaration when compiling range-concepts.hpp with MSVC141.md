# #66 - Error C4596: 'difference_type': illegal qualified name in member declaration when compiling range/concepts.hpp with MSVC141 [Closed]

> Username: ncook-hxgn  
> Created at: 2018-03-01 09:18:19 UTC  
> Updated at: 2018-07-13 08:35:46 UTC  
> Closed at: 2018-07-12 13:45:40 UTC  
> Url: https://github.com/boostorg/range/issues/66  

Hi there,  
  
I've been compiling boost 1.66.0 with MSVC 141 (Visual Studio 2017 15.5.6).   
When I do, I get Error C4596: 'difference_type': illegal qualified name in member declaration. This is caused by the namespace prefix at line 259 of concepts.hpp. (I'm assuming it's not a Visual Studio bug)  
`             BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;`  
  
I've created a pull request, #65, with what I think is a fix, it works for me and passes CI.  
Please let me know if the fix is suitable/appropriate. If it is, is there any chance it will make boost 1.67 ?  
  
Thanks,  
  
Nathan

---

## Comment 1

> Username: DanielaE  
> Created at: 2018-04-08 17:13:53 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-379566127  

@ncook-vero , @neilgroves ,  
this change causes lots of compile failures in my runs of the test suite. For details see my comments to #65

---

## Comment 2

> Username: sav-ix  
> Created at: 2018-04-21 15:51:32 UTC  
> Updated at: 2018-04-21 16:20:13 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-383307792  

Hello, everyone,  
  
For builds using MSVC 2017 15.5.0 got errors:  
```  
compile-c-c++ bin.v2\libs\date_time\build\msvc-14.1\debug\address-model-64\threading-multi\gregorian\greg_month.obj  
  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\date_time\build\msvc-14.1\debug\address-model-64\threading-multi\gregorian\greg_month.obj.rsp"   
  
greg_month.cpp  
c:\libBOOST-1.68.0-dev\build\boost/range/concepts.hpp(257): error C3646: 'n': unknown override specifier  
c:\libBOOST-1.68.0-dev\build\boost/range/concepts.hpp(264): note: see reference to class template instantiation 'boost::range_detail::RandomAccessIteratorConcept<Iterator>' being compiled  
c:\libBOOST-1.68.0-dev\build\boost/range/concepts.hpp(257): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
...failed compile-c-c++ bin.v2\libs\date_time\build\msvc-14.1\debug\address-model-64\threading-multi\gregorian\greg_month.obj...  
```  
Reproduced after PR 795046f8fc30e359d0a4826cd5bb652d3ccf3855 and f1906e914eaaf76408a0a4e69c61c457c08abba8 were merged.  
  
A workaround:  
```  
diff --git a/libs/range/include/boost/range/concepts.hpp b/libs/range/include/boost/range/concepts.hpp  
index 6fef2ea..de36a22 100644  
--- a/libs/range/include/boost/range/concepts.hpp  
+++ b/libs/range/include/boost/range/concepts.hpp  
@@ -253,7 +253,7 @@ namespace boost {  
              }  
          private:  
  // MSVC 14.1 - avoid C4596: 'difference_type': illegal qualified name in member declaration  
- #if defined(_MSC_VER) && _MSC_VER >= 1912   
+ #if defined(_MSC_FULL_VER) && _MSC_FULL_VER > 191225830  
              BOOST_DEDUCED_TYPENAME difference_type n;  
  #else  
              BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;  
```  
fixed it for me.  
In case it would be considered as a solution, a condition `_MSC_FULL_VER > 191225830` should be updated in order to correspond MSVC version, which introduced this issue.

---

## Comment 3

> Username: DanielaE  
> Created at: 2018-04-21 16:21:38 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-383310204  

Did you bother to check with current (or even still-to-be-released daily) versions of MSVC? According to my tests, at least every version beginning with 19.13 (i.e. VS 15.6) and later fails to compile with the PR in place. So, only a few 15.5.x releases require the workaround from the PR. Therefore, it's application **must** be restricted to this short range, i.e.  
`#if defined(_MSC_FULL_VER) && _MSC_FULL_VER > 191225830 && _MSC_FULL_VER < 191300000`  
it the proper constraint.

---

## Comment 4

> Username: sav-ix  
> Created at: 2018-04-21 16:39:56 UTC  
> Updated at: 2018-04-21 16:40:36 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-383311422  

Didn't check later MSVC versions yet. Assuming 15.5.6, mentioned in 1st post, should be fine.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-04-26 15:11:41 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-384677148  

`typename difference_type n;` is ill-formed; it might be better to use something correct instead. Perhaps `typename RandomAccessIteratorConcept<Iterator>::difference_type n;` or `typename BidirectionalIteratorConcept<Iterator>::difference_type n;`

---

## Comment 6

> Username: ncook-hxgn  
> Created at: 2018-04-30 08:44:23 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-385341472  

I do like the idea of doing it correctly, apologies for my ill-formed solution - possibly it wasn't the solution I thought it was.  
  
Of your suggestions, I prefer `typename RandomAccessIteratorConcept<Iterator>::difference_type n;` as it seems like the smallest change and doesn't introduce the question of what a `BidirectionalIteratorConcept` is to my mind - I wasn't trying to change or improve anything, I just wanted it to compile.   
  
Are you guys doing other work on this version of boost for this compiler version?  
Is it worth doing, or am I distracting you from more useful things?  
  
TL;DR: Should we just upgrade our boost to 1.67 (which says it's fine with VS2017) ?

---

## Comment 7

> Username: pabristow  
> Created at: 2018-05-23 16:30:19 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-391412806  

I have this problem on develop branch recently updated and current VS 15.7.2  
      typename RandomAccessIteratorConcept<Iterator>::difference_type n;  
and  
     BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept<Iterator>::difference_type n;  
both work for me, FWIW.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-06-30 15:45:32 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-401548805  

Can someone please fix this?  It completely breaks Multiprecision apart from anything else.

---

## Comment 9

> Username: sav-ix  
> Created at: 2018-07-08 10:01:50 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403276570  

+1 to something should be done at last.  
After moving to MSVC 2017 15.7.3 got errors:  
```  
.\boost/range/concepts.hpp(257): error C3646: 'n': unknown override specifier  
.\boost/range/concepts.hpp(264): note: see reference to class template instantiation 'boost::range_detail::RandomAccessIteratorConcept<Iterator>' being compiled  
.\boost/range/concepts.hpp(257): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
```  
A possible solution:  
- revert https://github.com/boostorg/range/pull/65/commits/f1906e914eaaf76408a0a4e69c61c457c08abba8 and https://github.com/boostorg/range/pull/65/commits/795046f8fc30e359d0a4826cd5bb652d3ccf3855 (this fixes builds using MSVC 2017 15.7.x and do not break builds using mingw-w64 8.1.0 and Windows ICC 2018 Update 3),  
- close this issue as out of scope (it was a compiler bug in MSVC 2017 15.5.x and should not affect Boost code),  
- advice to all Boost Users, who experience this error, to upgrade their build tools.  
  
How's that, @ncook-vero ?

---

## Comment 10

> Username: ncook-hxgn  
> Created at: 2018-07-09 08:42:10 UTC  
> Updated at: 2018-07-09 08:46:49 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403404859  

Hi @sav-ix, we (Vero) have now upgraded to VS 2017 15.7.4, so we should be fine with your suggestion if we can build boost with it in it's unadultered-by-myself form - we prefer that really..  
  
Essentially, I think @DanielaE hit the nail on the head: "Therefore, it's application must be restricted to this short range". You could either fix what appears to be an edge case, or advise upgrading build tools.  
(BTW, FYI, we did need the fix under VS 2017 15.6.6)  
  
I know what I'd do - advise upgrade and keep clean code :)  
  
Let me build a Boost 1.67 without my changes with VS 15.7.4, and I shall get back to you ASAP.

---

## Comment 11

> Username: pabristow  
> Created at: 2018-07-09 11:24:11 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403446684  

I'm confused.  Is Peter Dimov wrong when he says the original was ill-formed?  Sounds unlikely but this is above my paygrade?  
I'd like the current VS at the time of release of 1.68 to work with the current VS version at that release time (and to work with the then current develop branch too of course).  It's a show stopper for Multiprecision (and other ?)

---

## Comment 12

> Username: pdimov  
> Created at: 2018-07-09 11:26:39 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403447233  

The original was fine; the fix was ill-formed (which is why it doesn't work now.)

---

## Comment 13

> Username: ncook-hxgn  
> Created at: 2018-07-09 12:19:11 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403459617  

@pdimov @sav-ix  - I still experience this with VS 2017 15.7.4.  
Please see screenshot below demonstrating this. Please excuse my poor redactions of our closed source.  
  
![image](https://user-images.githubusercontent.com/36889813/42449944-72e47884-837a-11e8-890a-8f7d7fa3523b.png)  
  
However, my ill-formed fix continues to be a fix for this, for us at least, it would appear.

---

## Comment 14

> Username: pdimov  
> Created at: 2018-07-09 12:41:30 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403465213  

Do you have a small program I can try?

---

## Comment 15

> Username: ncook-hxgn  
> Created at: 2018-07-09 12:44:27 UTC  
> Updated at: 2018-07-09 13:02:01 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403466008  

It's not very small I'm afraid, and it's rather closed source. I'll see if I can put together a reproduction. outside of our product.  
I'm also juggling a V8 upgrade and various rebases today. I will get back to you A.S.A.P  
  
  
Edit: It seems the offending line of code in our product that is causing this error is the following function call to boost/geometry. I did actually raise an issue with boost::geometry who sent me here in the first place.  
  
`boost::geometry::get_turns<false, false, boost::geometry::detail::overlay::assign_null_policy>(inner, outer, strategy_inner_outer, robust_policy, turns, policy);`  
  
I'm not sure I'll be able to post an example outside of our production code, the code calling the fn (that when compiled is causing the problem) is rather maths-heavy, and alas, I am not. I shall see if I can reproduce it with that one line somehow..

---

## Comment 16

> Username: jzmaddock  
> Created at: 2018-07-09 16:48:42 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403544761  

>However, my ill-formed fix continues to be a fix for this, for us at least, it would appear.  
  
Well this is very strange, because for me with a fresh install of 15.7.4 and a program that consist of just:  
  
```  
#include <boost/range/concepts.hpp>  
```  
  
I get the:  
  
```  
1>c:\data\boost\boost\boost\range\concepts.hpp(257): error C3646: 'n': unknown override specifier  
```  
  
error.  
  
This is debug, x64, no pre-compiled headers.

---

## Comment 17

> Username: pdimov  
> Created at: 2018-07-09 17:04:28 UTC  
> Updated at: 2018-07-09 17:20:11 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403549481  

Interesting. That `#include` compiles for me both with and without `/permissive-`.  
  
Edit: no, my mistake, it fails with `/permissive-` with the error above.

---

## Comment 18

> Username: jzmaddock  
> Created at: 2018-07-09 17:24:25 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403555707  

>Edit: no, my mistake, it fails with /permissive- with the error above.  
  
Got it.  /permissive- is the default for new projects it seems so existing projects may not see this yet.

---

## Comment 19

> Username: pdimov  
> Created at: 2018-07-09 17:45:12 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403562132  

OK, @ncook-vero, does https://github.com/boostorg/range/pull/70 work for you?

---

## Comment 20

> Username: ncook-hxgn  
> Created at: 2018-07-10 08:43:54 UTC  
> Updated at: 2018-07-10 09:05:19 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403747183  

Ok, here's what I've been trying - building a boost 1.67 containing one of these `difference_type` definitions, then building our project against it. Here's my sketchpad:  
  
    //#if defined(_MSC_VER) && _MSC_VER >= 1913  
    //		BOOST_DEDUCED_TYPENAME difference_type n;  
    //#else  
    //		BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;  
    //		BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept<Iterator>::difference_type n;  
		        BOOST_DEDUCED_TYPENAME BidirectionalIteratorConcept<Iterator>::difference_type n;  
    //#endif  
  
#70 didn't work for me I'm afraid:  
`BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;` yields `Error	C4596	'difference_type': illegal qualified name in member declaration (compiling source file xxxxxxx.cpp)	xxxxxxx d:\development\xxxxxxx\3rdparty\boost\boost\range\concepts.hpp	258`  
  
`BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept<Iterator>::difference_type n;` yields `d:\development\xxxxxxx\3rdparty\boost\boost\range\concepts.hpp(258): error C4596: 'difference_type': illegal qualified name in member declaration (compiling source file xxxxxxx.cpp)  
`  
  
However, based on a previous suggestion, I tried  
`BOOST_DEDUCED_TYPENAME BidirectionalIteratorConcept<Iterator>::difference_type n;` and it worked. Given that this is essentially asking the parent class and template type to provide the definition (via ADL etc?), I think this essentially demonstrates the goodness of my original fix (I think it is essentially asking the compiler the same thing, isn't it?).   
  
Am I missing something?  
  
RE: /permissive-, we compile with /WAll, warnings-as-errors, etc. I saw /Zc:rvalueCast mentioned in the /permissive- MSDN page as turned on by /permissive-  .. I also saw /Zc:rvalueCast in our project settings, so we are compiling with /permissive-

---

## Comment 21

> Username: pdimov  
> Created at: 2018-07-10 09:17:02 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403756847  

OK, I'll do another PR with `BidirectionalIterator` instead.

---

## Comment 22

> Username: pdimov  
> Created at: 2018-07-10 09:18:48 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403757372  

No success in reproducing the problem in a self-contained test?

---

## Comment 23

> Username: ncook-hxgn  
> Created at: 2018-07-10 09:34:26 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403761834  

I'm attempting to create one now similar to @jzmaddock, just need to rebuild boost

---

## Comment 24

> Username: ncook-hxgn  
> Created at: 2018-07-10 11:06:54 UTC  
> Updated at: 2018-07-10 13:45:28 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-403785474  

Reporting back on a repro outside of our product..  
  
VS2017 15.7.4, Boost 1.67  
  
In a harness (new Win32 console app), it is my change in concepts.hpp that causes the `error C3646: 'n' : unknown override specifier` - so I think I have reproduced what everyone else is seeing. The harness was compiled with /WAll /WX /permissive- /Yu in debug and /W4 /WX /permissive- /Yu in release (x64 only).   
  
Worth noting that using `BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;` in place of my change (i.e., the original code) fixes the `error C3646: 'n' : unknown override specifier` in the harness.  
  
However, returning to our product which includes the very same header, with the same original-code-not-my-fix experimental change from the harness, the result is somewhat reversed and I get `error C4596: 'difference_type': illegal qualified name in member declaration ...`  
  
  
Edit Edit:  
  
The problem can be reproduced in a new VS2017 Win32 x64 project by disabling /permissive-.  
this doesn't happen including concepts.hpp on it's own, it seems it is something we are doing with the boost::geometry library - here's the offensive main.cpp  
  
    // TestBoostConcepts.cpp : Defines the entry point for the console application.  
    //  
      
    #include "stdafx.h"  
    // /WAll :  
    #pragma warning(disable: 4365)  
    #pragma warning(disable: 4371)  
    #pragma warning(disable: 4571)  
    #pragma warning(disable: 4514)  
    #pragma warning(disable: 4619)  
    #pragma warning(disable: 4625)  
    #pragma warning(disable: 4668)  
    #pragma warning(disable: 4626)  
    #pragma warning(disable: 4686)  
    #pragma warning(disable: 4710)  
    #pragma warning(disable: 4774)  
    #pragma warning(disable: 4820)  
    #pragma warning(disable: 5026)  
    #pragma warning(disable: 5027)  
      
    #pragma warning(disable: 4005)  
    #pragma warning(disable: 4100)  
    #pragma warning(disable: 5031)  
    #pragma warning(disable: 5032)  
      
    #include <boost/assign.hpp>  
    #include <boost/geometry/geometries/point_xy.hpp>  
    #include <boost/geometry/geometries/linestring.hpp>   
    #include <boost/geometry/algorithms/intersects.hpp>  
    #include <boost/geometry/algorithms/append.hpp>  
      
      
    namespace boost  
    {  
    	namespace geometry  
    	{  
    		namespace dispatch  
    		{  
      
    			template <bool Reverse, typename LineString, typename TurnPolicy>  
    			struct self_get_turn_points<Reverse, linestring_tag, LineString, TurnPolicy>  
    				: detail::self_get_turn_points::get_turns<Reverse, TurnPolicy>  
    			{  
    			};  
    		}  
    	}  
    }  
      
    int main()  
    {  
    	using point_t = boost::geometry::model::d2::point_xy<double>;  
    	using linestring_t = boost::geometry::model::linestring<point_t>;  
      
    	using turn_info = boost::geometry::detail::overlay::turn_info<point_t, boost::geometry::segment_ratio<double>>;  
    	using rescale_policy_t = boost::geometry::detail::no_rescale_policy;  
    	using interrupt_policy_t = boost::geometry::detail::self_get_turn_points::no_interrupt_policy;  
      
    	using named_linestring = std::pair<size_t, linestring_t>;  
      
    	using strategy_t = typename boost::geometry::strategy::relate::services::default_strategy<std::list<named_linestring>::value_type::second_type, std::list<named_linestring>::value_type::second_type>::type;  
      
    	//  
    	std::list<named_linestring> inners;  
      
    	rescale_policy_t robust_policy;  
    	interrupt_policy_t policy;  
      
    	strategy_t strategy_inner;  
    	std::deque<turn_info> turns;  
    	for (const auto& inner_pair : inners)  
    	{  
    		const auto& inner = inner_pair.second;  
    		boost::geometry::self_turns<boost::geometry::detail::overlay::assign_null_policy>(inner, strategy_inner, robust_policy, turns, policy);  
    	}  
      
        return 0;  
    }  
      
      
Command line to fail:  
`/Yu"stdafx.h" /GS- /GL /Wall /Gy /Zc:wchar_t /I"D:\Development\3rdParty\boost" /Zi /Gm- /O2 /Fd"x64\Release\vc141.pdb" /Zc:inline /fp:precise /D "NDEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /GF /WX /Zc:forScope /Gd /Oi /MD /FC /Fa"x64\Release\" /EHa /nologo /Fo"x64\Release\" /Fp"x64\Release\TestBoostConcepts.pch" /diagnostics:classic `  
  
Command line to succeed:  
`/permissive- /Yu"stdafx.h" /GS- /GL /Wall /Gy /Zc:wchar_t /I"D:\Development\3rdParty\boost" /Zi /Gm- /O2 /Fd"x64\Release\vc141.pdb" /Zc:inline /fp:precise /D "NDEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /GF /WX /Zc:forScope /Gd /Oi /MD /FC /Fa"x64\Release\" /EHa /nologo /Fo"x64\Release\" /Fp"x64\Release\TestBoostConcepts.pch" /diagnostics:classic `

---

## Comment 25

> Username: Flamefire  
> Created at: 2018-07-11 14:28:34 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404189842  

Why is `BOOST_DEDUCED_TYPENAME difference_type n;` used and not simply `difference_type n;`?

---

## Comment 26

> Username: vinniefalco  
> Created at: 2018-07-11 14:34:43 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404192145  

This is still a problem in 1.68 beta 1. Attempting to build all the boost libraries on my machine with /permissive- produces this output https://gist.github.com/vinniefalco/40b2e8e6a283377330258ce1e3281b6c

---

## Comment 27

> Username: Flamefire  
> Created at: 2018-07-11 14:41:29 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404194507  

@vinniefalco Would you mind testing if removing the `BOOST_DEDUCED_TYPENAME` from the line at `C:\Users\vinnie\src\boost\boost/range/concepts.hpp(257)` (and only there) solves this for you?

---

## Comment 28

> Username: vinniefalco  
> Created at: 2018-07-11 14:50:50 UTC  
> Updated at: 2018-07-11 14:51:36 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404197954  

Removing BOOST_DEDUCED_TYPENAME still produces an error. However, changing it as follows resolves the error (I am on the latest VS)  
```  
#if defined(_MSC_VER) && _MSC_VER >= 1912   
    BOOST_DEDUCED_TYPENAME  
    BidirectionalIteratorConcept<Iterator>::difference_type amount;  
 #else  
    BOOST_DEDUCED_TYPENAME  
    RandomAccessIteratorConcept::difference_type amount;  
 #endif  
```

---

## Comment 29

> Username: Flamefire  
> Created at: 2018-07-11 14:54:51 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404199469  

Does it produce the same error or a different one? And lastly: Would you mind testing `BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type amount;` too (or remove the first part of the `#if`)? If that works than previous discussion is right: The work-around should only be for a specific MSVC version or completely removed as the non-MSVC code is actually correct.

---

## Comment 30

> Username: vinniefalco  
> Created at: 2018-07-11 14:58:12 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404200711  

* Same error  
  
* `BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;` also compiles correctly

---

## Comment 31

> Username: vinniefalco  
> Created at: 2018-07-11 15:09:27 UTC  
> Updated at: 2018-07-11 15:09:37 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404204776  

All the boost libraries build correctly with  
```  
BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n  
```

---

## Comment 32

> Username: Flamefire  
> Created at: 2018-07-11 15:10:19 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404205038  

Just seen that develop already contains the fixes: #70 and #71  
This does not require macros. To summarize everything said and confirmed so far it is a bug in `_MSC_VER==1912` only (original fix said >= 1912, but others mentioned 1913 is not affected and a more fine grained `#if defined(_MSC_FULL_VER) && _MSC_FULL_VER > 191225830 && _MSC_FULL_VER < 191300000` was suggested)  
  
Please get #70 and #71 merged into master for the 1.68 version and this can be closed.

---

## Comment 33

> Username: ncook-hxgn  
> Created at: 2018-07-12 07:41:32 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404419811  

Okay (seems likely that I would stumble into a Visual Studio bug, that is my kind of luck), but what about our failed build? Simply removing `/permissive-` made it fail at that location - granted that the example code is mad..

---

## Comment 34

> Username: Flamefire  
> Created at: 2018-07-12 08:09:06 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404427400  

Did you test with those 2 PRs? Or simply replace the full `#if` stuff here with `BOOST_DEDUCED_TYPENAME BidirectionalIteratorConcept<Iterator>::difference_type n;` and test that. Are there still errors in your build then? If so, could you post that error? Would be very interesting!

---

## Comment 35

> Username: ncook-hxgn  
> Created at: 2018-07-12 08:25:08 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404431792  

I did, see above posts. I've tried all the permutations offered.

---

## Comment 36

> Username: pdimov  
> Created at: 2018-07-12 08:31:49 UTC  
> Updated at: 2018-07-12 08:32:10 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404433650  

You wrote above  
  
> However, based on a previous suggestion, I tried `BOOST_DEDUCED_TYPENAME BidirectionalIteratorConcept<Iterator>::difference_type n;` and it worked.  
  
Is this not true?

---

## Comment 37

> Username: ncook-hxgn  
> Created at: 2018-07-12 09:54:47 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404457953  

It is true.  At least I think it is. I suggest trying my ` TestBoostConcepts.cpp` sample in a VS 2017 15.7.4, toggling /permissive- in project settings, and trying the various difference_type definitions in concepts.hpp, that's what I did. /permissive- seemed to be key to the issue. Some of our projects use /permissive- and some don't. We need boost to work with all of them. The TestBoostConcepts.cpp code comes from a section of code in a project that is not compiled with /permissive-.

---

## Comment 38

> Username: Flamefire  
> Created at: 2018-07-12 10:31:55 UTC  
> Updated at: 2018-07-12 10:32:01 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404467803  

So where is the problem? If `BOOST_DEDUCED_TYPENAME BidirectionalIteratorConcept<Iterator>::difference_type n;` works for you then you did not test what I suggested in https://github.com/boostorg/range/issues/66#issuecomment-404427400

---

## Comment 39

> Username: pdimov  
> Created at: 2018-07-12 10:52:31 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404472496  

Unfortunately, your sample compiles for me no matter which definition of `n` I use in `concepts.hpp`. My command line does not match exactly:  
  
```  
/GS /GL /W4 /Gy /Zc:wchar_t /I"/boost-git/develop" /Zi /Gm- /Ox /Ob2 /Fd"x64\Release\vc141.pdb" /Zc:inline /fp:precise /D "NDEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /Gd /Oi /MD /FC /Fa"x64\Release\" /EHsc /nologo /Fo"x64\Release\" /Fp"x64\Release\testbed2017.pch" /diagnostics:classic   
```  
  
but I don't see how any of those options would affect the front end. What do you have in `stdafx.h`?

---

## Comment 40

> Username: pdimov  
> Created at: 2018-07-12 10:55:21 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404473235  

In either case, the develop branch now contains the `BidirectionalIteratorConcept` definition, so if you could check that your product compiles with it, we'd be done.

---

## Comment 41

> Username: ncook-hxgn  
> Created at: 2018-07-12 11:01:55 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404474990  

`stdafx.h` contains:  
  
    // stdafx.h : include file for standard system include files,  
    // or project specific include files that are used frequently, but  
    // are changed infrequently  
    //  
      
    #pragma once  
      
    #define _CRT_SECURE_NO_WARNINGS  
    #define _SCL_SECURE_NO_WARNINGS  
      
    #include "targetver.h"  
      
    // TODO: reference additional headers your program requires here  
  
I'll check again, bear with me. We are using /WAll /WX, so we must disable many warnings in the product, as in the sample. We disable others in the product, I just disabled what I had to so that the example would 'compile' at least as far as concepts.hpp.

---

## Comment 42

> Username: ncook-hxgn  
> Created at: 2018-07-12 12:26:02 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404494539  

This built successfully:  
  
             private:  
    #if defined(_MSC_VER) && _MSC_VER >= 1913  
        	BOOST_DEDUCED_TYPENAME BidirectionalIteratorConcept<Iterator>::difference_type n;  
    #else  
        	BOOST_DEDUCED_TYPENAME RandomAccessIteratorConcept::difference_type n;  
    #endif

---

## Comment 43

> Username: pdimov  
> Created at: 2018-07-12 13:34:43 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404513835  

Why didn't you use the current contents of the develop branch?  
  
https://github.com/boostorg/range/blob/70d1727ed301f2852ba911aacdcbcc57bb0ac92d/include/boost/range/concepts.hpp#L254-L257

---

## Comment 44

> Username: ncook-hxgn  
> Created at: 2018-07-12 13:36:45 UTC  
> Updated at: 2018-07-12 13:45:30 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404514433  

I'm `#if defined(_MSC_VER) && _MSC_VER >= 1913`  
  
(Because it takes a long time to clone boost and I don't have time, tbh, never mind the whole FOSS version approval process and whatnot. Apologies).  
  
Am I the only one who can close this? Closing.

---

## Comment 45

> Username: Flamefire  
> Created at: 2018-07-12 13:42:45 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404516339  

Can this be simply closed please? The bug is obviously fixed/handled in develop and working for everyone so far.

---

## Comment 46

> Username: pdimov  
> Created at: 2018-07-12 14:03:37 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404523297  

OK, I've taken the liberty of merging the fix to master.

---

## Comment 47

> Username: Flamefire  
> Created at: 2018-07-12 14:29:25 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404532125  

Great! Make sure it gets included in the 1.68 release, please.

---

## Comment 48

> Username: neilgroves  
> Created at: 2018-07-12 14:55:45 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404541210  

Peter,  
  
Thank you enormously for your massive help in this. I was massively time  
constrained. Your help is really appreciated.  
  
Thanks,  
Neil  
  
On 12 July 2018 at 15:03, Peter Dimov <notifications@github.com> wrote:  
  
> OK, I've taken the liberty of merging the fix to master.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/issues/66#issuecomment-404523297>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AGaE6m39SjBTDb7wxJkw_PAK0GjFpgShks5uF1c6gaJpZM4SX_Fa>  
> .  
>

---

## Comment 49

> Username: pabristow  
> Created at: 2018-07-13 08:29:47 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404764960  

As well as thanks to/from everyone, would it be useful to record why this concept fix, especially why bidirectional rather than randomaccess?  
  
Paul  
  
  
From: neilgroves [mailto:notifications@github.com]  
Sent: 12 July 2018 15:56  
To: boostorg/range  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/range] Error C4596: 'difference_type': illegal qualified name in member declaration when compiling range/concepts.hpp with MSVC141 (#66)  
  
Peter,  
  
Thank you enormously for your massive help in this. I was massively time  
constrained. Your help is really appreciated.  
  
Thanks,  
Neil  
  
On 12 July 2018 at 15:03, Peter Dimov <notifications@github.com> wrote:  
  
> OK, I've taken the liberty of merging the fix to master.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/issues/66#issuecomment-404523297>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AGaE6m39SjBTDb7wxJkw_PAK0GjFpgShks5uF1c6gaJpZM4SX_Fa>  
> .  
>  
  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/range/issues/66#issuecomment-404541210>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAdiMK9rkKb26UiaKFB4M3Gdsna6hks5uF2NzgaJpZM4SX_Fa>.

---

## Comment 50

> Username: Flamefire  
> Created at: 2018-07-13 08:35:46 UTC  
> Url: https://github.com/boostorg/range/issues/66#issuecomment-404766456  

> As well as thanks to/from everyone, would it be useful to record why this concept fix, especially why bidirectional rather than randomaccess?  
  
The type is not defined in the current type but in the parent type like here:  
  
```  
template<class T>  
struct Foo{  
  using type = T;  
};  
template<class T>  
struct Bar: public Foo<T>{  
  static typename Foo<T>::type value = T(0);  
};  
```  
  
Not sure if `static typename Bar::type value = T(0);` is covered by the standard, but at least MSVC chocked on that (in at least 1 version) and I remember similar problems in other libraries too (using the parent type instead of the current fixed a problem)
