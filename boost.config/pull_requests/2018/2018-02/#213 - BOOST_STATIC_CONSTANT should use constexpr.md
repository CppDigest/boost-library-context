# #213 BOOST_STATIC_CONSTANT should use constexpr [Merged]

> Username: Kojoley  
> Created at: 2018-02-24 17:17:51 UTC  
> Updated at: 2018-03-23 23:52:55 UTC  
> Merged at: 2018-03-16 18:24:59 UTC  
> Closed at: 2018-03-16 18:24:59 UTC  
> Url: https://github.com/boostorg/config/pull/213  



---

## Comment 1

> Username: Lastique  
> Created_at: 2018-03-18 10:06:54 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373986138  

This broke at least Boost.MPL and all dependent libraries:  
  
```  
gcc.compile.c++ bin.v2/libs/thread/build/gcc-4.7/debug/threadapi-pthread/threading-multi/pthread/once.o  
In file included from ./boost/thread/detail/invoke.hpp:32:0,  
                 from ./boost/thread/pthread/once_atomic.hpp:17,  
                 from ./boost/thread/once.hpp:26,  
                 from libs/thread/src/pthread/./once_atomic.cpp:9,  
                 from libs/thread/src/pthread/once.cpp:8:  
./boost/mpl/bool.hpp:34:23: error: redeclaration ‘mpl_::bool_<B>::value’ differs in ‘constexpr’  
./boost/mpl/bool.hpp:25:5: error: from previous declaration ‘value’  
./boost/mpl/bool.hpp:34:23: error: declaration of ‘constexpr const bool value’ outside of class is not definition [-fpermissive]  
    "g++-4.7"   -std=c++11 -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -g -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX  -I"." -c -o "bin.v2/libs/thread/build/gcc-4.7/debug/threadapi-pthread/threading-multi/pthread/once.o" "libs/thread/src/pthread/once.cpp"  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-03-18 10:55:02 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373988751  

Thanks for the heads up - I've reverted the change - if someone wants to re-submit a new PR that fixes the problem that's fine, otherwise I think I'll leave well alone...

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-03-18 10:56:13 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373988820  

BTW, is there any use case where constexpr delivers something that the good old `static const T = ` does not?  I'm struggling to think of one.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-03-18 10:59:18 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373989006  

It guarantees that your constant is computable at compile time so you will get error message on definition rather then on usage.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-03-18 11:11:51 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373989732  

>It guarantees that your constant is computable at compile time so you will get error message on definition rather then on usage.  
  
Assuming you're using BOOST_STATIC_CONST at class scope, which is really the intended usage, that's true in any case surely?  There may be exceptions for literal classes - but since these are C++11 or later anyway, you might as well just be using constexpr directly.  
  
There are a lot of corner cases - for example from C++17 we could add the `inline` declaration so that an out-of-line definition isn't required and so on...

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-03-18 11:58:21 UTC  
> Updated_at: 2018-03-18 12:06:52 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373992661  

I think we should deprecate `BOOST_STATIC_CONSTANT` in favor of `static BOOST_CONSTEXPR_OR_CONST`. That way at least the declaration and definition can be made consistent more easily. Enum workaround is no longer relevant, I think.  
  
Regarding C++17 inline variables, we need `BOOST_CXX17_INLINE_VARIABLE` (I have one in Boost.Atomic already), but that is another PR.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-03-18 12:06:47 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373993235  

> Assuming you're using BOOST_STATIC_CONST at class scope, which is really the intended usage, that's true in any case surely? There may be exceptions for literal classes  
  
I do not see any.  
  
> If a static data member is of const literal type, its declaration in the class definition can specify a brace-orequal-initializer in which every initializer-clause that is an assignment-expression is a constant expression.  
> A static data member of literal type can be declared in the class definition with the constexpr specifier; if so, its declaration shall specify a brace-or-equal-initializer in which every initializer-clause that is an assignment-expression is a constant expression. [ Note: In both these cases, the member may appear in constant expressions. — end note ] The member shall still be defined in a namespace scope if it is odrused (3.2) in the program and the namespace scope definition shall not contain an initializer.  
  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2011/n3242.pdf § 9.4.2/3  
  
```cpp  
struct Foo {  
  int i;  
  int j;  
};  
  
struct Bar {  
  static constexpr Foo foo = /*Foo*/{};  
};  
```

---

## Comment 8

> Username: Lastique  
> Created_at: 2018-03-18 12:18:57 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373994024  

@Kojoley This is only possible in C++11 and later. C++03 [class.static.data]/4 only allows in-class initializer for const integral or const enumeration types. If you're writing C++11 code, you don't need `BOOST_STATIC_CONSTANT`.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-03-18 12:22:23 UTC  
> Updated_at: 2018-03-18 12:23:35 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373994234  

I just answered @jzmaddock question.  
There was no literal classes in C++03 http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2005/n1905.pdf 2.13.

---

## Comment 10

> Username: Lastique  
> Created_at: 2018-03-18 12:25:51 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373994483  

I think his point was that replacing `const` with `constexpr` in the context of `BOOST_STATIC_CONSTANT` does not offer any benefits. And it doesn't, except to make the code look more modern.

---

## Comment 11

> Username: Kojoley  
> Created_at: 2018-03-18 13:03:04 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373997064  

It looks like you are right. I though this would be caught at compile time, but it does not:  
```cpp  
struct A   
{  
    static constexpr int value = 123;  
    void foo(const int &) { }  
    void bar() { foo(value); }  
};  
  
int main()  
{  
    A a;  
    a.bar();  
}  
```

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2018-03-18 13:34:57 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-373999642  

>I think his point was that replacing const with constexpr in the context of BOOST_STATIC_CONSTANT does not offer any benefits. And it doesn't, except to make the code look more modern.  
  
Yes that was exactly my point.  And if it's all hidden behind a macro then it doesn't really look very modern either :(  
  
A similar situation exists with `inline` declared class variables:  
  
* If you're targeting C++17 and later just go ahead and use them.  
* If you're targeting earlier versions, then an out-of-line definition will be required in some use contexts, so you might as well forget the "inline" for C++17 and just go ahead and provide the definition.  If you start macro-izing the code with different branches for pre and post C++17, then you end up with more typing not less - which rather defeats the whole point of the feature.

---

## Comment 13

> Username: Lastique  
> Created_at: 2018-03-18 13:44:45 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-374000334  

C++17 inline variables can be used not only as static class data members. In Boost.Atomic, I'm using them to declare namespace-scope constants. The code compiles whether these constants are declared inline or not, but there's a subtle difference in linkage, which may be significant in some cases.  
  
https://github.com/boostorg/atomic/blob/develop/include/boost/memory_order.hpp#L57

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-03-23 22:32:30 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-375815497  

constexpr is inline anyway.  
  
In C++17, the out-of-class definition of the constexpr member variable is deprecated, too. It's neverending fun.

---

## Comment 15

> Username: Lastique  
> Created_at: 2018-03-23 22:53:53 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-375819147  

> constexpr is inline anyway.  
  
Is it? I haven't found where it is said so.

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-03-23 23:09:35 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-375821576  

"A function or static data member declared with the constexpr specifier is implicitly an inline function or variable ([dcl.inline])."  
  
http://eel.is/c++draft/dcl.constexpr#1

---

## Comment 17

> Username: Lastique  
> Created_at: 2018-03-23 23:13:44 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-375822180  

Oh, you meant a static data member. I thought you meant any constexpr variables.

---

## Comment 18

> Username: pdimov  
> Created_at: 2018-03-23 23:52:55 UTC  
> Url: https://github.com/boostorg/config/pull/213#issuecomment-375827247  

I think I remember suggestions to make all namespace-scope or static constexpr inline (makes sense to me), but apparently the CWG decided against that.  
  
Here's the deprecation reference: http://eel.is/c++draft/depr.static_constexpr

---
