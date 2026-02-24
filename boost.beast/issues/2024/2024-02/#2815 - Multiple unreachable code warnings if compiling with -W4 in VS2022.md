# #2815 - Multiple unreachable code warnings if compiling with /W4 in VS2022 [Closed]

> Username: pps83  
> Created at: 2024-02-11 12:30:45 UTC  
> Updated at: 2024-02-11 17:43:57 UTC  
> Closed at: 2024-02-11 17:43:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2815  

Basic beast `boost_1_84_0` code produces multiple unreachable code warnings if compiling with /W4 in VS2022:  
  
```  
1>------ Build started: Project: boostbeast-warn, Configuration: Debug x64 ------  
1>boostbeast-warn.cpp  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
1>D:\work-pps\boost_1_84_0\boost\beast\core\impl\buffers_cat.hpp(186): warning C4702: unreachable code  
```  
  
Here's full project to repro: https://github.com/pps83/boostbeast-warn  
It's blank new console app, the only thing I changed is set `/W4` for warnings

---

## Comment 1

> Username: pps83  
> Created at: 2024-02-11 12:31:30 UTC  
> Updated at: 2024-02-11 12:47:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2815#issuecomment-1937688348  

This one seems to be related. It was fixed, but then [later ppl said](https://github.com/boostorg/beast/issues/1582#issuecomment-1357452971) it's resurfaced: https://github.com/boostorg/beast/issues/1582

---

## Comment 2

> Username: pps83  
> Created at: 2024-02-11 13:09:28 UTC  
> Updated at: 2024-02-11 14:17:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2815#issuecomment-1937743402  

Locally, I've made this change to get rid of the annoying warning:  
  
```  
#ifdef _MSC_VER  
#pragma warning(push)  
#pragma warning(disable:4702)  
#endif  
        template<class I>  
        reference operator()(I)  
        {  
            return *self.it_.template get<I::value>();  
        }  
#ifdef _MSC_VER  
#pragma warning(pop)  
#endif  
```

---

## Comment 3

> Username: ashtum  
> Created at: 2024-02-11 16:58:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2815#issuecomment-1937808609  

Thank you for reporting. This has been fixed in #2803 and will be included in boost 1.85.

---

## Comment 4

> Username: pps83  
> Created at: 2024-02-11 17:43:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2815#issuecomment-1937820139  

I tried #2803 and it does fix the error. Thanks
