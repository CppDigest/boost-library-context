# #42 - Build failure MSVC 14 (x86) on Boost 1.68.0 [Open]

> Username: stefan-floeren  
> Created at: 2018-08-27 07:02:04 UTC  
> Updated at: 2020-08-04 03:57:19 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42  

Hi,  
  
After upgrading to Boost 1.68.0, builds using lockfree fail on x86; x64 works.  
  
Compiler version:  
  
    Microsoft (R) C/C++ Optimizing Compiler Version 19.00.24215.1 for x86  
    Copyright (C) Microsoft Corporation.  All rights reserved.  
  
On trying to compile the test cases, I get this error (complete log attached):  
```  
stack_bounded_stress_test.cpp  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(508): error C2719: '_Val': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(669): note: see reference to class template instantiation 'std::_Atomic_base<_Ty,8>' being compiled  
        with  
        [  
            _Ty=boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<boost::lockfree::stack<long>::node,std::allocator<boost::lockfree::stack<long>::node>>::freelist_node>  
        ]  
T:\boost_1_68_0\boost/lockfree/detail/freelist.hpp(255): note: see reference to class template instantiation 'std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<T,Alloc>::freelist_node>>' being compiled  
        with  
        [  
            T=boost::lockfree::stack<long>::node,  
            Alloc=std::allocator<boost::lockfree::stack<long>::node>  
        ]  
T:\boost_1_68_0\boost/lockfree/stack.hpp(100): note: see reference to class template instantiation 'boost::lockfree::detail::freelist_stack<T,Alloc>' being compiled  
        with  
        [  
            T=boost::lockfree::stack<long>::node,  
            Alloc=std::allocator<boost::lockfree::stack<long>::node>  
        ]  
stack_bounded_stress_test.cpp(25): note: see reference to class template instantiation 'boost::lockfree::stack<long>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
T:\boost_1_68_0\boost/bind/placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(519): error C2719: '_Right': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(525): error C2719: '_Right': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(542): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(548): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(580): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(587): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(594): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(602): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(610): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(617): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(624): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(632): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(640): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(647): error C2719: '_Value': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(680): error C2719: '_Val': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(686): error C2719: '_Right': formal parameter with requested alignment of 8 won't be aligned  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\atomic(691): error C2719: '_Right': formal parameter with requested alignment of 8 won't be aligned  
```  
  
Could you please have a look on how to fix this issue?  
  
[Complete log](https://github.com/boostorg/lockfree/files/2322722/output.log)

---

## Comment 1

> Username: timblechmann  
> Created at: 2018-08-27 09:26:38 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-416169397  

hmm, i don't have an msvc2015 32bit devenv at hand atm, but could you test:  
  
```  
diff --git a/include/boost/lockfree/detail/freelist.hpp b/include/boost/lockfree/detail/freelist.hpp  
index 374b0c7..266a0a6 100644  
--- a/include/boost/lockfree/detail/freelist.hpp  
+++ b/include/boost/lockfree/detail/freelist.hpp  
@@ -38,6 +38,7 @@ namespace detail   {  
 template <typename T,  
           typename Alloc = std::allocator<T>  
          >  
+BOOST_ALIGNMENT( 8 )  
 class freelist_stack:  
     Alloc  
 {  
diff --git a/include/boost/lockfree/stack.hpp b/include/boost/lockfree/stack.hpp  
index b9e43df..3efcfc4 100644  
--- a/include/boost/lockfree/stack.hpp  
+++ b/include/boost/lockfree/stack.hpp  
@@ -66,6 +66,7 @@ template <typename T, class A0, class A1, class A2>  
 #else  
 template <typename T, typename ...Options>  
 #endif  
+BOOST_ALIGNMENT( 8 )  
 class stack  
 {  
 private:  
```  
?

---

## Comment 2

> Username: stefan-floeren  
> Created at: 2018-08-27 10:18:31 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-416182035  

The same error ([out.log](https://github.com/boostorg/lockfree/files/2323449/out2.log)).  
  
Note that I used the following patch, consistent with the other uses of `BOOST_ALIGNMENT`, as I get an `INTERNAL COMPILER ERROR` with your patch.  
  
```  
diff --git a/include/boost/lockfree/detail/freelist.hpp b/include/boost/lockfree/detail/freelist.hpp  
index 374b0c7..77fe14d 100644  
--- a/include/boost/lockfree/detail/freelist.hpp  
+++ b/include/boost/lockfree/detail/freelist.hpp  
@@ -38,7 +38,9 @@ namespace detail   {  
 template <typename T,  
           typename Alloc = std::allocator<T>  
          >  
-class freelist_stack:  
+class  
+BOOST_ALIGNMENT( 8 )  
+    freelist_stack:  
     Alloc  
 {  
     struct freelist_node  
diff --git a/include/boost/lockfree/stack.hpp b/include/boost/lockfree/stack.hpp  
index b9e43df..3de124d 100644  
--- a/include/boost/lockfree/stack.hpp  
+++ b/include/boost/lockfree/stack.hpp  
@@ -66,7 +66,9 @@ template <typename T, class A0, class A1, class A2>  
 #else  
 template <typename T, typename ...Options>  
 #endif  
-class stack  
+class  
+BOOST_ALIGNMENT( 8 )  
+    stack  
 {  
 private:  
 #ifndef BOOST_DOXYGEN_INVOKED  
```

---

## Comment 3

> Username: tobias-loew  
> Created at: 2018-08-29 20:40:52 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-417098312  

I've got the same problems with msvc 2015 x86. The problem is that msvc 2015 x86 doesn't support alignment of 8 for function parameters passed on the stack. (AFAIK they're only guaranteed to be aligned at 4-bytes and there's no way to specify parameter-alignment.)

---

## Comment 4

> Username: timblechmann  
> Created at: 2018-08-30 04:18:23 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-417184902  

hmm, i've tested msvc 2017 ia32, which builds fine :/

---

## Comment 5

> Username: tobias-loew  
> Created at: 2018-08-30 07:28:53 UTC  
> Updated at: 2018-08-30 07:32:28 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-417218227  

I can also confirm that msvc 2017 x86 (and of course also x64) supports alignment for function parameters.   
But msvc 2015 has issues:  
- x86 doesn't support alignment at all (only 4-byte alignment is guaranteed (even for types that have a larger "natural" alignment))  
- x64 supports function parameters up to "alignas(32)"; ("alignas(64)" results again in an error C2719)  
  
IMHO removing the alignment request for msvc  <= 2015  (x86) would be the best.  
  
Just to be complete. msvc 2017 supports alignment for function parameter up to alignas(64) for both x86 and x64.

---

## Comment 6

> Username: timblechmann  
> Created at: 2018-08-30 09:29:59 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-417254119  

i've fixed a few issues of aligned function parameters, but i cannot see in the error message, which function fails to instantiate due to parameter alignment :/  
  
removing alignment hints is not an option here, as it's `std::atomic`, that is aligned.  
  
but does it help to force the use of boost.atomic?

---

## Comment 7

> Username: tobias-loew  
> Created at: 2018-08-30 10:42:22 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-417274686  

Yes, switching to boost.atomic (and adding boost namespace to all the memory_order_... solves the problem!  
The reason is: in std::atomic, atomic::atomic(_Ty _Val) and atomic::operator=(_Ty _Right) takes its argument by value, while boost.atomic takes args > sizeof(void*) by const-ref!

---

## Comment 8

> Username: mmakhalaf  
> Created at: 2019-01-22 12:19:06 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-456379688  

We had this problem as well using `boost::lockfree::queue`.  
  
As @tobias-loew did,  we've added  `#define BOOST_LOCKFREE_FORCE_BOOST_ATOMIC` before the include fixes the compiler error for us. Then undef'ing at the end of the header.  
  
Are there any hidden caveats to doing that?  
  
Thanks

---

## Comment 9

> Username: rooong  
> Created at: 2020-08-04 03:56:50 UTC  
> Updated at: 2020-08-04 03:57:19 UTC  
> Url: https://github.com/boostorg/lockfree/issues/42#issuecomment-668365443  

I found this problem in vs2013, the really reason maybe in files:prefix.hpp and tagged_ptr_ptrcompression.hpp  
the macro defined  
in boost 1.67:  
it is: #ifdef BOOST_ARCH_X86_64  
but in boost 1.68  
it is:#if BOOST_ARCH_X86_64 || defined (__aarch64__)  
  
we can change to **#if defined(BOOST_ARCH_X86_64) || defined (__aarch64__)** before the include fixes the compiler error for us
