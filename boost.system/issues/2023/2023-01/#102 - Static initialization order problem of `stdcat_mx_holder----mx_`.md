# #102 - Static initialization order problem of `stdcat_mx_holder<>::mx_` [Closed]

> Username: trueqbit  
> Created at: 2023-01-18 19:45:45 UTC  
> Updated at: 2023-01-22 21:09:08 UTC  
> Closed at: 2023-01-22 00:21:01 UTC  
> Url: https://github.com/boostorg/system/issues/102  

In commit [#986efb142035](https://github.com/boostorg/system/commit/986efb1420354c2565b63dbbd0830b1d5a191eab#diff-6207c1ddc0f386e770ce61bc248135696a0848b804fe61d0b7cf9335dd6000a5) you switched to using a global static `std::mutex` when converting a `boost::system:error_category` to a `std::error_category`.  
  
This is subject to the static initialization order fiasco when a program sets up a static std::error_category variable based on a boost::system::error_category.  
  
E.g. even this simple program leads to a crash with Visual C++ 17.4.3, because the compiler decides to initialize the static variable `ec1` before `boost::system::detail::stdcat_mx_holder<>::mx_`, and `boost::system::error_category::init_stdcat()` locks an uninitialized mutex:  
  
```c++  
#include <system_error>  
#include <boost/asio.hpp>  
  
const std::error_category& ec1 = boost::asio::error::get_misc_category();  
  
int main() {  
    return 0;  
}  
```  
  
Callstack:  
```  
 	msvcp140d.dll!00007ffd01062c20()	Unknown  
 	msvcp140d.dll!00007ffd01063155()	Unknown  
>	boost_error_category_issue.exe!std::_Mutex_base::lock() Line 50	C++  
 	boost_error_category_issue.exe!std::lock_guard<std::mutex>::lock_guard<std::mutex>(std::mutex & _Mtx={...}) Line 428	C++  
 	boost_error_category_issue.exe!boost::system::error_category::init_stdcat() Line 141	C++  
 	boost_error_category_issue.exe!boost::system::error_category::operator std::error_category const &() Line 193	C++  
 	boost_error_category_issue.exe!`dynamic initializer for 'ec1''() Line 4	C++  
 	ucrtbased.dll!00007ffcfe381fb3()	Unknown  
 	boost_error_category_issue.exe!__scrt_common_main_seh() Line 258	C++  
 	boost_error_category_issue.exe!__scrt_common_main() Line 331	C++  
 	boost_error_category_issue.exe!mainCRTStartup(void * __formal=0x0000004e477a9000) Line 17	C++  
 	kernel32.dll!BaseThreadInitThunk()	Unknown  
 	ntdll.dll!00007ffdc8c2485b()	Unknown  
  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-18 22:50:44 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1396198882  

The constructor of `std::mutex` is `constexpr`, so this shouldn't be possible - in principle - but MSVC has been known to not respect this. I'll see what I can do.

---

## Comment 2

> Username: trueqbit  
> Created at: 2023-01-19 10:21:58 UTC  
> Updated at: 2023-01-19 10:22:38 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1396742881  

Seems like a stupid problem that's surfacing now.  
  
This is suboptimal, but how about wrapping the `std::mutex` in a function call, for Microsoft's STL only of course?  
I know I could do that in my programs too. The advantage of doing it in the library would be to avoid surprises, and it has a very low runtime cost - it's not like you'd convert millions of `boost::system::error_category`S to `std::error_category`S in a hot code path.  
  
Something along these lines:  
```c++  
#if defined(_MSVC_STL_VERSION) && (_MSVC_STL_VERSION < 144)  
template<class = void> std::mutex& stdcat_mx() noexcept  
{  
    static std::mutex mx;  
    return mx;  
}  
#else  
template<class = void> struct stdcat_mx_holder  
{  
    static std::mutex mx_;  
};  
  
template<class T> std::mutex stdcat_mx_holder<T>::mx_;  
  
inline std::mutex& stdcat_mx() noexcept  
{  
    return stdcat_mx_holder<>::mx_;  
}  
#endif  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2023-01-19 17:09:49 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1397321895  

I suppose that's possible, yes. I had something different in mind - [use `std::shared_mutex` under MSVC](https://github.com/boostorg/system/commit/8449c62162e497043623c1c872b28f934f1545c9), because it's an SRWLock and actually has a constexpr default constructor.  
  
This doesn't work for VS 2013, though, where I might need to apply your suggestion. (Or just mark the test as failed and pretend VS 2013 doesn't exist.)

---

## Comment 4

> Username: trueqbit  
> Created at: 2023-01-19 22:00:50 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1397666213  

Oh, that's a neat trick too!  
  
I don't care about VS 2013, but I don't know about the boost policies. The question is how much all these legacy compilers are in use. vcpkg, for example, requires VC 2015 Update 3 as a minimal base C++14 compiler, which is problematic enough these days.  
Anyway, you could do a cascading fallback.  
  
In any case, I would use an inline/template method as an additional abstraction layer that returns a `lock_guard` instead of the mutex. This encapsulates things very well for the `init_stdcat()` function, where you don't have to worry about the mutex type.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-01-20 18:00:20 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1398749239  

Should be fixed on develop now for msvc-12.0 as well, by https://github.com/boostorg/system/commit/71ee26c1888bf3ae57f173e2b41b322a36add823.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-01-22 00:21:01 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1399364439  

Unless you have something further to add, I consider this fixed. Thanks for the report.

---

## Comment 7

> Username: trueqbit  
> Created at: 2023-01-22 21:09:08 UTC  
> Url: https://github.com/boostorg/system/issues/102#issuecomment-1399609087  

Thx Peter for your outstanding response!  
I can hardly wait for Boost 1.82 :)
