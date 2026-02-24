# #30 - boost__type_index__abi mismatch when linking clang-cl.exe & cl.exe objects with RTTI disabled [Closed]

> Username: k15tfu  
> Created at: 2020-09-09 09:08:34 UTC  
> Updated at: 2020-12-22 06:11:13 UTC  
> Closed at: 2020-12-21 16:17:34 UTC  
> Url: https://github.com/boostorg/type_index/issues/30  

Hi!  
  
We use clang-cl.exe in our proejct and link it against MSVC-compiled 3rd-party libraries. Both (our objects and .lib files) are compiled with /GR- but nevetheless we are getting a linker error:  
```  
1>lld-link : error : /failifmismatch: mismatch detected for 'boost__type_index__abi':  
1>>>> Foo\foo.obj has value RTTI is off - using CTTI  
1>>>> libBar.lib(bar.obj) has value RTTI is off - typeid() is used only for templates  
```  
  
Probably the mismatch detection code (https://github.com/boostorg/type_index/blob/1dacdb561ed8ddd27e1e2418b678236eaf5f3683/include/boost/type_index.hpp#L33) should also consider this case as valid, or there should be a way to skip this check. Thoughts?

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-21 16:17:34 UTC  
> Url: https://github.com/boostorg/type_index/issues/30#issuecomment-749056630  

There is a way to skip this check, see https://www.boost.org/doc/libs/1_64_0/doc/html/BOOST_TYPE_INDEX_FORCE_NO_RTTI_COMPATIBILITY.html  
  
Please, reopen the ticket if that does not help

---

## Comment 2

> Username: k15tfu  
> Created at: 2020-12-22 06:11:13 UTC  
> Url: https://github.com/boostorg/type_index/issues/30#issuecomment-749361680  

@apolukhin It doesn't work due to `|| defined(BOOST_MSVC)` condition: https://github.com/boostorg/type_index/blob/9862011b6b5e900eee31d32684766c15d3506adf/include/boost/type_index.hpp#L28  
  
In the clang-cl.exe case we get `RTTI is off - using CTTI`, but cl.exe leads to `RTTI is off - typeid() is used only for templates`.
