# #323 - boost::future_errc not usable [Open]

> Username: steffenb7333  
> Created at: 2020-07-14 08:12:00 UTC  
> Updated at: 2023-10-27 09:12:25 UTC  
> Url: https://github.com/boostorg/thread/issues/323  

Compiler: gcc 9.3.0, clang++ 9.0.1 / c++17 mode  
Boost: 1.73  
  
```  
#include <boost/thread.hpp>  
auto future_errc_compile_error(const boost::future_error& e)  
{  
	return e.code() == boost::future_errc::broken_promise;  
}  
...  
```  
  
This fails with  
  
> /usr/include/boost/system/error_code.hpp:595:32: error: 'make_error_code' was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
> /usr/include/boost/thread/futures/future_error_code.hpp:47:5: note: 'boost::system::error_code boost::system::make_error_code(boost::future_errc)' declared here, later in the translation unit  
>    47 |     make_error_code(future_errc e) BOOST_NOEXCEPT

---

## Comment 1

> Username: kuzkry  
> Created at: 2023-08-14 19:27:54 UTC  
> Updated at: 2023-08-14 19:37:03 UTC  
> Url: https://github.com/boostorg/thread/issues/323#issuecomment-1677936615  

Hi, I investigated this a little bit and discovered that it can be simplified to such code:  
  
```  
namespace boost {  
namespace system {  
  
class error_code  
{  
public:  
  constexpr error_code() noexcept = default;  
  
  template<class ErrorCodeEnum>  
  constexpr error_code( ErrorCodeEnum e ) noexcept  
  {  
    *this = make_error_code( e );  
  }  
};  
} // namespace system  
  
enum class future_errc  
{  
  broken_promise = 1  
};  
  
namespace system  
{  
inline error_code make_error_code(future_errc e) noexcept  
{  
  return error_code{};  
}  
} // namespace system  
} // namespace boost  
  
int main()  
{  
  boost::system::error_code(boost::future_errc::broken_promise);  
}  
```  
  
My understanding is that ADL cannot find `future_errc` inside `boost::system` and a potentially matching declaration of `make_error_code` is found later than used.  
I don't think we can really fix the problem by adding a declaration above its use because `error_code` is defined inside `boost::system`, which is already a dependency for `boost::thread` - so `boost::system` code should contain no references to `boost::thread` unfortunately.  
  
I figured out we could trick ADL a little bit by introducing a helper namespace and I'm trying to deliver a PR #390  based on that idea.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-09-11 12:42:59 UTC  
> Url: https://github.com/boostorg/thread/issues/323#issuecomment-1713803081  

`make_error_code` should be defined in the namespace of the enum, and not in `boost::system`. There's no need for a helper namespace.

---

## Comment 3

> Username: kuzkry  
> Created at: 2023-10-27 09:12:25 UTC  
> Url: https://github.com/boostorg/thread/issues/323#issuecomment-1782573359  

Hi @pdimov, thanks your interest and sorry for the long wait.  
  
Yes, you're right that `make_error_code` should be there (at least originally), but I didn't want to break existing code that someone might rely on, thus I implemented this helper namespace for backward compatibility.  
  
So... I'm just pointing that out but I'm open for making an exception here and do it like you proposed so the code is more simplified. Please let me know what you think.
