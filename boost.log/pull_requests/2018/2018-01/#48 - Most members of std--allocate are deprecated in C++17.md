# #48 Most members of std::allocate are deprecated in C++17 [Closed]

> Username: DanielaE  
> Created at: 2018-01-02 17:39:47 UTC  
> Updated at: 2018-01-03 12:31:30 UTC  
> Closed at: 2018-01-03 11:12:54 UTC  
> Url: https://github.com/boostorg/log/pull/48  

Replace them by their cousins from std::allocator_traits. In addition to that, std::allocator<void> (used as default argument to the 'AllocatorT' template parameter of some public types) is deprecated too, and needs some sort of emulation. The emulation type is a mere placeholder without ever being used. Without that, heaps of deprecation warnings will fall onto humble users when compiling with MSVC 15 in C++17 mode.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-01-02 22:36:43 UTC  
> Url: https://github.com/boostorg/log/pull/48#issuecomment-354893882  

I don't really like this. Code with sprinkled `ifdef`s  is harder to maintain and I would rather avoid the `none` trick, especially with duplicate definitions of the type. Regarding `allocator_traits`, I would rather universally use `allocator_traits`, which is either defined by the standard library or Boost.Log (in the latter case the definition has to be provided in a separate internal header).  
  
Regarding the `allocator<void>` specialization, doesn't the code still work without it but when `allocator_traits` are used to interact with the allocator? I think it should, and in that case I would prefer to leave that part as is.

---

## Comment 2

> Username: DanielaE  
> Created_at: 2018-01-03 08:39:55 UTC  
> Url: https://github.com/boostorg/log/pull/48#issuecomment-354960069  

I totally understand your objections. OTOH, I need a short-term (i.e. 'yesterday') solution for a Boost 1.66 derivative which compiles with `/std:c++latest /permissive- /W4 /WX` using MSVC15.5 or later. Without that I can't run tests with our own in-house software to qualify these newer compilers for production. Just about an hour ago I achieved this goal: I can run the full Boost test-suite under the given conditions.  
  
Regarding `std::allocator_traits`: I don't feel like re-implementing them for C++98/03 compilers. At least not in the near future - we just have no need for that. We have left those old standards behind us, and I rather go for C++14 as baseline.  
  
Regarding `std::allocator<void>`: I ran Boost.Log's test-suite _with_ `std::allocator_traits`, but `std::allocator<void>` as template default arguments still in place. In this case, users will see compiler messages like this one:  
```  
compile-c-c++ Z:\boost\bin.v2\libs\log\example\doc\msvc-14.1\dbg\adrs-mdl-64\thrdp-wn32\thrd-mlt\expressions_channel_severity_filter.obj  
expressions_channel_severity_filter.cpp  
..\example\doc\expressions_channel_severity_filter.cpp(61,78): warning C4996: 'std::allocator<void>': warning STL4009: std::allocator<void> is deprecated in C++17. You can define _SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
    typedef expr::channel_severity_filter_actor< std::string, severity_level > min_severity_filter;  
                                                                             ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.12.25827\include\xmemory0(1087): note: see declaration of 'std::allocator<void>'  
	{	// generic allocator for type void  
```  
If you look at `log\example\doc\expressions_channel_severity_filter.cpp` you will notice that there is no `std::allocator<void>` anywhere in the code. So, users of the library are not to blame for outcomes like above but rather have to find means to deal with it.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-01-03 11:12:54 UTC  
> Url: https://github.com/boostorg/log/pull/48#issuecomment-354988099  

Merely specifying `std::allocator<void>` as a default template argument and then rebinding the allocator using `std::allocator_traits` should not cause `std::allocator<void>` instantiation, so even without the deprecated specialization the code should be valid. I think MSVC is incorrect when it issues the warning when the specialization is not instantiated but merely referenced like that, so I would suggest reporting it to Microsoft Connect. I will probably leave that part as is and recommend defining the macro as a workaround.  
  
Regarding `std::allocator_traits`, I guess I'll implement it later myself when I have some time.  
  
In any case, thank you for the report and the proposed PR.

---

## Comment 4

> Username: DanielaE  
> Created_at: 2018-01-03 12:13:37 UTC  
> Url: https://github.com/boostorg/log/pull/48#issuecomment-354998196  

Well, whatever you like. BTW, Adam made me aware of `boost::container::allocator_traits<>`, a C++03 implementation of `allocator_traits<>` by Ion. May be this is a candidate to be moved to Boost.Detail.

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-01-03 12:31:30 UTC  
> Url: https://github.com/boostorg/log/pull/48#issuecomment-355001182  

Thanks, `boost::container::allocator_traits` might be a good alternative for C++03.

---
