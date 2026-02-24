# #70 - Address sanitizer complains when exceptions are thrown [Closed]

> Username: HK47196  
> Created at: 2017-12-09 21:35:12 UTC  
> Updated at: 2017-12-10 13:52:38 UTC  
> Closed at: 2017-12-10 13:52:38 UTC  
> Url: https://github.com/boostorg/context/issues/70  

minimal example:  
  
```cxx  
#include <boost/context/all.hpp>  
  
namespace bcx = boost::context;  
  
int main(){  
  auto c = bcx::callcc([](bcx::continuation&& c_){  
    try {  
      throw 5;  
    } catch(const int&){  
    }  
    return std::move(c_);  
  });  
}  
```  
  
output:  
  
```  
==21820==WARNING: ASan is ignoring requested __asan_handle_no_return: stack top: 0x7ffc47270000; bottom 0x631000023000; size: 0x1cec4724d000 (31801131454464)  
False positive error reports may follow  
For details see https://github.com/google/sanitizers/issues/189  
```  
  
clang v5.0.0 and gcc 7.2.1, boost 1.65.1

---

## Comment 1

> Username: olk  
> Created at: 2017-12-09 21:49:35 UTC  
> Url: https://github.com/boostorg/context/issues/70#issuecomment-350507976  

It's not an error  
  
Am 09.12.2017 22:35 schrieb "rsw0x" <notifications@github.com>:  
  
> minimal example:  
>  
> #include <boost/context/all.hpp>  
> namespace bcx = boost::context;  
> int main(){  
>   auto c = bcx::callcc([](bcx::continuation&& c_){  
>     try {  
>       throw 5;  
>     } catch(const int&){  
>     }  
>     return std::move(c_);  
>   });  
> }  
>  
> output:  
>  
> ==21820==WARNING: ASan is ignoring requested __asan_handle_no_return: stack top: 0x7ffc47270000; bottom 0x631000023000; size: 0x1cec4724d000 (31801131454464)  
> False positive error reports may follow  
> For details see https://github.com/google/sanitizers/issues/189  
>  
> clang v5.0.0 and gcc 7.2.1, boost 1.65.1  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/context/issues/70>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QHmbb_sXIFD5RfAzuM0bSGvmGE1sks5s-v0QgaJpZM4Q8QoO>  
> .  
>

---

## Comment 2

> Username: olk  
> Created at: 2017-12-10 13:52:07 UTC  
> Url: https://github.com/boostorg/context/issues/70#issuecomment-350549853  

You have to use ucontext as implementation and apply BOOST_USE_ASAN:  
`b2 cxxflags="-fsanitize=address -std=c++11 context-impl=ucontext -DBOOST_USE_ASAN" linkflags="-lasan" `  
  
but you still get the warning:  
==10360==WARNING: ASan doesn't fully support makecontext/swapcontext functions and may produce false positives in some cases!
