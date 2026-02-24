# #3 - test/by.cpp fails to compile on OS X [Closed]

> Username: bwoods  
> Created at: 2015-02-14 22:48:51 UTC  
> Updated at: 2015-02-15 15:42:38 UTC  
> Closed at: 2015-02-15 15:42:38 UTC  
> Url: https://github.com/boostorg/hof/issues/3  

```  
$make by  
[100%] Building CXX object CMakeFiles/by.dir/test/by.cpp.o  
fit/test/by.cpp:24:20: error: no matching function for call to object of type  
      'by_adaptor<std::__1::__mem_fn<int foo::*>, fit::operators::add>'  
    FIT_TEST_CHECK(fit::by(std::mem_fn(&foo::x), add)(foo(1), foo(2)) == 3);  
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
fit/test/test.h:66:35: note: expanded from macro 'FIT_TEST_CHECK'  
#define FIT_TEST_CHECK(...) if (!(__VA_ARGS__)) std::cout << "*****FAILED: " << #__VA_ARGS__ << "@" << __FILE__ << ": " << __LINE__ ...  
                                  ^  
fit/./fit/by.h:86:20: note: candidate template ignored: substitution failure [with Ts = <foo, foo>]: no  
      matching function for call to object of type 'const std::__1::__mem_fn<int foo::*>'  
    constexpr auto operator()(Ts&&... xs) const FIT_RETURNS  
                   ^  
1 error generated.  
make[3]: *** [CMakeFiles/by.dir/test/by.cpp.o] Error 1  
make[2]: *** [CMakeFiles/by.dir/all] Error 2  
make[1]: *** [CMakeFiles/by.dir/rule] Error 2  
make: *** [by] Error 2  
```  
  
> **Compiler**:  
> Apple LLVM version 6.0 (clang-600.0.56) (based on LLVM 3.5svn)  
> Target: x86_64-apple-darwin14.1.0  
> Thread model: posix

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-02-15 06:08:12 UTC  
> Url: https://github.com/boostorg/hof/issues/3#issuecomment-74405638  

I'll look into this.   
  
BTW, Is this the only test that fails? You can run all the tests using `make check`

---

## Comment 2

> Username: bwoods  
> Created at: 2015-02-15 07:02:47 UTC  
> Url: https://github.com/boostorg/hof/issues/3#issuecomment-74406717  

Yes. All the others compile clean and pass.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-02-15 15:42:38 UTC  
> Url: https://github.com/boostorg/hof/issues/3#issuecomment-74422290  

Alright, thanks. Its fixed on master now. If you are using `std::mem_fn` you will need to wrap it in a `mutable_` adaptor first, because in libc++, it doesn't fulfill the requirements for a `FunctionObject`. I am not sure what the standard requires for `mem_fn`.
