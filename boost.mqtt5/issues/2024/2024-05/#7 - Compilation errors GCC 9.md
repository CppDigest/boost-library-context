# #7 - Compilation errors GCC 9 [Closed]

> Username: amcharhal  
> Created at: 2024-05-07 07:09:33 UTC  
> Updated at: 2024-05-09 13:12:26 UTC  
> Closed at: 2024-05-09 13:12:26 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/7  

I encountered numerous compilation errors when attempting to compile the example code you provided. Interestingly, it compiles without issues on other devices.  
  
Here are the specifications of the host where the code fails to compile:  
  
**Ubuntu 20.04  
GCC version 9.4.0  
C++1**7  
I noticed that you tested it only with GCC 10. While it should theoretically work with C++17, if any features from C++20 are utilized, it might cause compatibility problems

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-05-09 13:12:26 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/7#issuecomment-2102634396  

Thanks for reporting the issue!  
  
As it turns out, GCC in versions 9, 10 and 11 have inconsistent treatment of  `noexcept` move constructors. Clang and MSVC are consistent. Here's a simplified description of the problem:  
  
```  
  struct A {  
      A(A&&) = default;  
  };  
    
  struct B {  
      A a;  
      B(B&&) noexcept = default;  
  };  
```  
  
GCC will throw a compile-time error since `B` marks its move constructor as `noexcept` while `A` does not. Hence, `B`'s move constructor wouldn't throw, while `A`'s may, which is inconsistent. Clang and MSVC compile the above code in all versions (although strictly speaking, they shouldn't), while GCC in different versions does different things. You may look for details in the proposal [P1286R2](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1286r2.html).  
  
In our particular case, the problem was that Asio's bind structures do not mark their move constructors with `noexcept` while we use `noexcept` for move constructors in classes having these bind structures as members. Therefore, we were also inconsistent.  
  
The fix has already been pushed to the master branch.
