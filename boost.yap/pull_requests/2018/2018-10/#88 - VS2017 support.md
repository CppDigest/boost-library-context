# #88 VS2017 support [Merged]

> Username: Kojoley  
> Created at: 2018-10-08 15:44:33 UTC  
> Updated at: 2018-10-20 19:26:15 UTC  
> Merged at: 2018-10-20 19:26:15 UTC  
> Closed at: 2018-10-20 19:26:15 UTC  
> Url: https://github.com/boostorg/yap/pull/88  

With these fixes/workarounds the library is usable on msvc-14.1 and all tests pass.

---

## Review 1 [Commented]

> Username: tzlaine  
> Created_at: 2018-10-18 06:58:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/yap/pull/88#pullrequestreview-165947226  

📁 test/comma.cpp

```diff
 111 |              term<void_callable>{{&call_count, &void_called}}());
 112 | 
 115 |-         BOOST_MPL_ASSERT(
```

> Username: tzlaine  
> Created_at: 2018-10-18 06:58:44 UTC  
> Updated_at: 2018-10-18 16:30:25 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226187173  

Most of these changes look good, but why are you having trouble with BOOST_MPL_ASSERT?  It's widely used in Boost tests.  If it's a compilation error, please post the error.  Same if it's a warning too, I guess.

> Username: Kojoley  
> Created_at: 2018-10-18 15:52:56 UTC  
> Updated_at: 2018-10-18 16:30:25 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226363622  

```  
compile-c-c++ bin.v2\libs\yap\test\comma.test\msvc-14.1\debug\threading-multi\comma.obj  
comma.cpp  
libs\yap\test\comma.cpp(116): error C2955: 'std::is_same': use of class template requires template argument list  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.15.26726\include\xtr1common(86): note: see declaration of 'std::is_same'  
  
    call "bin.v2\standalone\msvc\msvc-14.1\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\yap\test\comma.test\msvc-14.1\debug\threading-multi\comma.obj.rsp"  
  
...failed compile-c-c++ bin.v2\libs\yap\test\comma.test\msvc-14.1\debug\threading-multi\comma.obj...  
```  
IIUC MSVC have some problems with `decltype` in macros.

> Username: tzlaine  
> Created_at: 2018-10-18 16:03:08 UTC  
> Updated_at: 2018-10-18 16:30:25 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226367809  

Well that sucks.  Is there any way to fix these tests on MSVC without removing MPL_ASSERT?  MPL_ASSERT is much nicer than static_assert, because when it fails you get the two types being compared printed out in the compiler diagnostics.

> Username: Kojoley  
> Created_at: 2018-10-18 16:13:17 UTC  
> Updated_at: 2018-10-18 16:30:25 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226371540  

You can move decltype out of macro and it will help  
```cpp  
        using type = decltype(evaluate(int_void_expr));  
        BOOST_MPL_ASSERT(  
            (std::is_same<void, type>));  
```

> Username: tzlaine  
> Created_at: 2018-10-18 16:19:57 UTC  
> Updated_at: 2018-10-18 16:30:25 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226373974  

Ok, could you reform your PR to do that?  Also, could you rebase the PR on top of my changes from yesterday?  I'd like to see Appveyor (newly added) show green for this PR before accepting.

> Username: Kojoley  
> Created_at: 2018-10-18 16:58:04 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226386779  

I did. Actually it is not needed to do rebase because travis/appveor makes it automatically (the only problem is lack of start build button on old PRs).

> Username: tzlaine  
> Created_at: 2018-10-18 17:18:45 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226393220  

Ok, thanks.  It looks like the Appveyor build is still not passing though.

> Username: Kojoley  
> Created_at: 2018-10-18 17:23:20 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226394636  

I do not see `/std:c++14` flag (AFAIK 14.15.26726 is not C++14 by default).

> Username: pdimov  
> Created_at: 2018-10-18 17:30:21 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226397002  

What else could it possibly be? :-)

> Username: pdimov  
> Created_at: 2018-10-18 17:35:54 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226398767  

For testing whether types are the same, you can also use `BOOST_TEST_TRAIT_TRUE` as in f.ex. https://github.com/boostorg/core/blob/develop/test/is_same_test.cpp#L24.

> Username: tzlaine  
> Created_at: 2018-10-18 17:45:48 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226402233  

That would work too, if it produces similar diagnostics.  Does it?

> Username: pdimov  
> Created_at: 2018-10-18 17:50:31 UTC  
> Updated_at: 2018-10-18 17:50:32 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226404230  

It's a runtime test, so not quite the same if you'd rather have compile-fail tests. The diagnostic looks like this: https://travis-ci.org/pdimov/variant2/jobs/443239342#L612

> Username: tzlaine  
> Created_at: 2018-10-18 17:56:41 UTC  
> Updated_at: 2018-10-18 17:56:42 UTC  
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226406465  

Ah, got it.  I think I'd rather stick with MPL_ASSERT then.


---

## Comment 2

> Username: tzlaine  
> Created_at: 2018-10-19 01:32:03 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431216294  

Any idea why the Appveyor build still fails?

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-10-19 01:40:02 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431217567  

The first error I see is  
```  
LINK : fatal error LNK1104: cannot open file 'libboost_serialization-vc141-mt-gd-x64-1_68.lib'   
```  
Not sure how it's supposed to work.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-10-19 01:40:24 UTC  
> Updated_at: 2018-10-19 01:40:59 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431217623  

```  
c:\projects\yap\example\lazy_vector.cpp(88): error C3779: 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []': a function that returns 'auto' cannot be used before it is defined [C:\projects\yap\build\example\lazy_vector.vcxproj]  
  c:\projects\yap\example\lazy_vector.cpp(44): note: see declaration of 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []'  
  c:\projects\yap\example\lazy_vector.cpp(103): note: see reference to function template instantiation 'lazy_vector &lazy_vector::operator +=<boost::yap::expr_kind::minus,tuple_type>(const lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type> &)' being compiled  
c:\projects\yap\example\lazy_vector.cpp(88): error C2088: '[': illegal for struct   
```  
  
```  
  c:\projects\yap\example\lazy_vector.cpp(88): error C3779: 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []': a function that returns 'auto' cannot be used before it is defined [C:\projects\yap\build\example\lazy_vector.vcxproj]  
  c:\projects\yap\example\lazy_vector.cpp(88): error C2088: '[': illegal for struct [C:\projects\yap\build\example\lazy_vector.vcxproj]  
"C:\projects\yap\build\ALL_BUILD.vcxproj" (default target) (1) ->  
"C:\projects\yap\build\perf\lazy_vector_perf.vcxproj" (default target) (27) ->  
  c:\projects\yap\perf\lazy_vector_perf.cpp(90): error C3779: 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []': a function that returns 'auto' cannot be used before it is defined [C:\projects\yap\build\perf\lazy_vector_perf.vcxproj]  
  c:\projects\yap\perf\lazy_vector_perf.cpp(90): error C2088: '[': illegal for struct [C:\projects\yap\build\perf\lazy_vector_perf.vcxproj]  
```  
  
I have only run tests under `test` folder with `b2` myself.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-19 01:46:28 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431218542  

Later on, there's this:  
```  
  lazy_vector.cpp  
CL : warning : the native Microsoft compiler is not supported due to lack of proper C++14 support. [C:\projects\yap\build\example\lazy_vector.vcxproj]  
CL : warning : Your compiler doesn't provide C++14 or higher capabilities. Try adding the compiler flag '-std=c++14' or '-std=c++1y'. [C:\projects\yap\build\example\lazy_vector.vcxproj]  
c:\projects\yap\example\lazy_vector.cpp(88): error C3779: 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []': a function that returns 'auto' cannot be used before it is defined [C:\projects\yap\build\example\lazy_vector.vcxproj]  
  c:\projects\yap\example\lazy_vector.cpp(44): note: see declaration of 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []'  
  c:\projects\yap\example\lazy_vector.cpp(103): note: see reference to function template instantiation 'lazy_vector &lazy_vector::operator +=<boost::yap::expr_kind::minus,tuple_type>(const lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type> &)' being compiled  
c:\projects\yap\example\lazy_vector.cpp(88): error C2088: '[': illegal for struct [C:\projects\yap\build\example\lazy_vector.vcxproj]  
Done Building Project "C:\projects\yap\build\example\lazy_vector.vcxproj" (default targets) -- FAILED.  
```  
No idea about this one either, but I wonder whether the 1.68 Hana works on 2017. There are VC-specific fixes on its develop branch.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-19 01:47:11 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431218650  

Tested it locally with the latest VS and got the same error  
```  
1>main.cpp(119): error C3779: 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []': a function that returns 'auto' cannot be used before it is defined  
1>main.cpp(74): note: see declaration of 'lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type>::operator []'  
1>main.cpp(143): note: see reference to function template instantiation 'lazy_vector &lazy_vector::operator +=<boost::yap::expr_kind::minus,tuple_type>(const lazy_vector_expr<boost::yap::expr_kind::minus,tuple_type> &)' being compiled  
1>main.cpp(119): error C2088: '[': illegal for struct  
```

---

## Comment 7

> Username: tzlaine  
> Created_at: 2018-10-19 01:52:56 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431219607  

Yeah, these look to me like the compiler is not applying the full-fledged rules for deduction.  
  
The Boost.Serialization bit looks like autolinking, possibly from my use of the Boost.Test headers.  I'm certainly not linking it in on purpose.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-10-19 01:57:09 UTC  
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431220198  

> Yeah, these look to me like the compiler is not applying the full-fledged rules for deduction.  
  
If I move definition into the struct it compiles. A simplified case with auto definition out of class works so I do not know what is broken in MSVC, but I am not surprised...  
  
> The Boost.Serialization bit looks like autolinking, possibly from my use of the Boost.Test headers. I'm certainly not linking it in on purpose.  
  
Serialization links because of https://github.com/boostorg/yap/blob/b9d6d347dd74b85efe28acf9f57a3544712b22a1/example/autodiff_library/autodiff.h#L14

---
