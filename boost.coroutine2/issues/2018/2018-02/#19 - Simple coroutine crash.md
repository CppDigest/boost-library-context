# #19 - Simple coroutine crash [Closed]

> Username: ghost  
> Created at: 2018-02-06 12:59:16 UTC  
> Updated at: 2019-01-26 05:16:45 UTC  
> Closed at: 2018-02-15 07:13:34 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19  

Following program crashes when compiled with Visual Studio 2015 Update 3, win32, release build. But does not in debug build or if cout is uncommented. Boost version 1.66  
```  
int main() {  
    typedef boost::coroutines2::coroutine<void> coro_t;  
    coro_t::pull_type coro([&](coro_t::push_type& yield) {  
        //std::cout << "111";  
    });  
    return 0;  
}  
```

---

## Comment 1

> Username: olk  
> Created at: 2018-02-06 18:36:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-363521043  

I don't use Windows, could you try your example with a larger stack size + uncommented cout?  
`coro_t::pull_type coro(fixedsize_stack(1024*1024), [&](coro_t::push_type& yield) {`

---

## Comment 2

> Username: ghost  
> Created at: 2018-02-06 20:34:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-363556383  

fixedsize_stack(1024*1024) doesn't change behaviour.  
I've also tried to run it in VS2017:  
```  
(boost\context\continuation_fcontext.hpp)  
  
transfer_t context_unwind( transfer_t t) {  
    throw forced_unwind( t.fctx);  <---- Unhandled exception at 0x761633AB (ucrtbase.dll) in CoroTest.exe: Fatal program exit requested.  
    return { nullptr, nullptr };  
}  
```

---

## Comment 3

> Username: olk  
> Created at: 2018-02-08 19:20:47 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-364219732  

I can't reproduce your problem. I've used msvc-14.1 (VS 2017) and built the test code in directory coroutine2/example.

---

## Comment 4

> Username: ghost  
> Created at: 2018-02-09 07:02:20 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-364352614  

Every example from coroutine2/example work fine, but my test code does not. Is empty coroutine prohibited? Project attached, I build it in release/x86 configuration.  
[CoroTest.zip](https://github.com/boostorg/coroutine2/files/1710018/CoroTest.zip)

---

## Comment 5

> Username: olk  
> Created at: 2018-02-10 16:45:31 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-364670316  

I'm not familair with MSVC/Windows but I guess your problem is related to incompatible compiler flags used to compile the boost libraries and the settings in you MSVC project.

---

## Comment 6

> Username: ghost  
> Created at: 2018-02-13 15:08:08 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-365294686  

Flags used to compile boost do not affect my issue. Problem goes away if I turn off global program optimization (/GL) or change /EHsc (default, tells the compiler to assume that functions declared as extern "C" never throw a C++ exception) to /EHs (tells the compiler to assume that functions declared as extern "C" may throw an exception).  Does boost throw exceptions from extern "C" functions?

---

## Comment 7

> Username: olk  
> Created at: 2018-02-15 07:09:52 UTC  
> Updated at: 2018-02-15 07:10:28 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-365842811  

yes, an exception might be thrown by ontop_fcontext() (declared as extern 'C') is used in the dtor if stack was not destructed.  
I'll add a hint to the documentation

---

## Comment 8

> Username: ghost  
> Created at: 2018-02-15 07:13:34 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-365843347  

Ok, thank you for help!

---

## Comment 9

> Username: olk  
> Created at: 2018-02-15 07:18:09 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-365844015  

thank you too

---

## Comment 10

> Username: devshgraphicsprogramming  
> Created at: 2019-01-25 17:51:42 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-457657814  

@olk any chance of using coroutine2/context with global program optimization under MSVC?

---

## Comment 11

> Username: olk  
> Created at: 2019-01-25 18:49:25 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-457680540  

@devshgraphicsprogramming : I don't know - becaue I don't use Windows

---

## Comment 12

> Username: devshgraphicsprogramming  
> Created at: 2019-01-25 19:34:50 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-457697852  

is there a particular reason `\GL` must be disabled?

---

## Comment 13

> Username: ghost  
> Created at: 2019-01-26 05:16:45 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/19#issuecomment-457803286  

Actually,  /EHs should be enough.
