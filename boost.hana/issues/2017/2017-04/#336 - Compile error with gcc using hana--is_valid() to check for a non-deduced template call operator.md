# #336 - Compile error with gcc using hana::is_valid() to check for a non-deduced template call operator [Closed]

> Username: otherjason  
> Created at: 2017-04-12 13:10:13 UTC  
> Updated at: 2017-04-23 19:38:41 UTC  
> Closed at: 2017-04-23 19:38:41 UTC  
> Url: https://github.com/boostorg/hana/issues/336  

I'm cross-posting this from [Stack Overflow](http://stackoverflow.com/questions/43352374/how-can-i-use-boost-hana-to-determine-whether-a-functor-has-a-call-operator-that), as no one there has pointed out a way in which I'm using Hana wrong, so I suspect it could be a compiler/Hana bug.  
  
In my application, I want to determine at compile time whether an arbitrary functor type `Func` has a nullary call operator that can be invoked with a given explicit template argument `T`. [Based on a previous SO answer that I found](http://stackoverflow.com/questions/35419045/boost-hana-how-to-check-if-function-has-specialisation-for-a-certain-type/35419693), I came up with the following:  
  
    #include <boost/hana.hpp>  
    #include <iostream>  
    #include <type_traits>  
      
    namespace hana = boost::hana;  
      
    namespace detail   
    {  
        template <typename T>  
        auto can_call = hana::is_valid([](auto &&f) ->   
            decltype(f.template operator()<T>()) { });  
    }  
      
    template <typename Func, typename T>  
    constexpr auto can_call() ->  
        decltype(detail::can_call<typename std::remove_reference<T>::type>(  
            std::declval<Func>())) { return {}; }  
      
    struct foo  
    {  
        template <typename T, typename =   
            std::enable_if_t<!std::is_same<T, char>::value>>  
        void operator()() const { }  
    };  
      
    int main()  
    {  
        std::cout << "char: " << can_call<foo, char>() << std::endl;  
        std::cout << "int: " << can_call<foo, int>() << std::endl;  
    }  
  
I would expect this example to print out:  
  
    char: 0  
    int: 1  
  
Since the `char` template argument type is explicitly `enable_if`-ed out in `foo`. I've tried the following compilers:  
  
- Apple clang v8.0.0: The example compiles and runs as expected.  
- mainline clang v3.9.1+ (via Wandbox): The example compiles and runs as expected.  
- mainline clang v3.6.0 - v3.8.1 (via Wandbox): The compiler dies with an internal error.  
- g++ 7.0 trunk, 20170410 (via Wandbox): The compilation fails with the following errors:  
   
        dd.cc: In instantiation of ‘auto detail::can_call<char>’:  
        dd.cc:15:14:   required by substitution of ‘template<class Func, class T> constexpr decltype (can_call<typename std::remove_reference<_To>::type>(declval<Func>())) can_call() [with Func = foo; T = char]’  
        dd.cc:25:50:   required from here  
        dd.cc:10:10: error: ‘auto detail::can_call<char>’ has incomplete type  
            auto can_call = hana::is_valid([](auto &&f) -> decltype(f.template operator()<T>()) { });  
                ^~~~~~~~  
        dd.cc: In function ‘int main()’:  
        dd.cc:25:50: error: no matching function for call to ‘can_call<foo, char>()’  
            std::cout << "char: " << can_call<foo, char>() << std::endl;  
                                                        ^  
        dd.cc:14:16: note: candidate: template<class Func, class T> constexpr decltype (can_call<typename std::remove_reference<_To>::type>(declval<Func>())) can_call()  
        constexpr auto can_call() ->  
                    ^~~~~~~~  
        dd.cc:14:16: note:   substitution of deduced template arguments resulted in errors seen above  
        dd.cc: In instantiation of ‘auto detail::can_call<int>’:  
        dd.cc:15:14:   required by substitution of ‘template<class Func, class T> constexpr decltype (can_call<typename std::remove_reference<_To>::type>(declval<Func>())) can_call() [with Func = foo; T = int]’  
        dd.cc:26:48:   required from here  
        dd.cc:10:10: error: ‘auto detail::can_call<int>’ has incomplete type  
            auto can_call = hana::is_valid([](auto &&f) -> decltype(f.template operator()<T>()) { });  
                ^~~~~~~~  
        dd.cc:26:48: error: no matching function for call to ‘can_call<foo, int>()’  
            std::cout << "int: " << can_call<foo, int>() << std::endl;  
                                                    ^  
        dd.cc:14:16: note: candidate: template<class Func, class T> constexpr decltype (can_call<typename std::remove_reference<_To>::type>(declval<Func>())) can_call()  
        constexpr auto can_call() ->  
                    ^~~~~~~~  
        dd.cc:14:16: note:   substitution of deduced template arguments resulted in errors seen above  
  
  
It seems to not like my use of `hana::is_valid()` to determine whether the specified operator exists. However, I think the way I'm using it is consistent with its intended use. It seems like there are a few potential explanations:  
  
- A gcc bug, which would be present in all versions that I've tested (I've also tried 6.3 as well as the 7.0 trunk). By definition, it seems that clang < 3.9.0 has a bug here since it ICEs.  
- This construct should not be allowed for some reason, and clang is being more accommodating than gcc in its interpretation of the standard.  
- I used Hana incorrectly in some way, or it's not capable of doing this specific type of check. If there's a better way to do this, I'm all for it!

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-04-12 17:22:26 UTC  
> Url: https://github.com/boostorg/hana/issues/336#issuecomment-293649002  

It crashes on Apple Clang 7.3 too. It can be worked around by not using a lambda in your `is_valid`. I can't say if that is the same problem with gcc 7.0 though.  
  
```cpp  
namespace detail  
{  
    template <typename T>  
    struct check_can_call {  
      template <typename F>  
      constexpr auto operator()(F&& f) ->  
        decltype(f.template operator()<T>()) { }  
    };  
  
    template <typename T>  
    constexpr auto can_call = hana::is_valid(check_can_call<T>{});  
}  
```

---

## Comment 2

> Username: otherjason  
> Created at: 2017-04-12 17:27:10 UTC  
> Url: https://github.com/boostorg/hana/issues/336#issuecomment-293650228  

Thanks for that suggestion. It looks like with the workaround you gave, it now compiles on clang 3.5.0+, like the rest of Hana. gcc 6.x/7.x still seems to reject it though. :disappointed:

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-04-12 17:40:41 UTC  
> Updated at: 2017-04-12 17:51:47 UTC  
> Url: https://github.com/boostorg/hana/issues/336#issuecomment-293653898  

Here's one that works on gcc 6-7 as well.  
```cpp  
namespace detail   
{  
    template <typename T>  
    struct check_can_call {   
      template <typename F>  
      constexpr auto operator()(F&& f) ->   
        decltype(f.template operator()<T>()) { }  
    };  
  
    template <typename T>  
    using is_call_valid = decltype(hana::is_valid(check_can_call<T>{}));  
  
    template <typename T>  
    constexpr is_call_valid<T> can_call{};  
}  
```  
It just adds a another layer of indirection 😎   
  
I'll add an answer to your SO question too.

---

## Comment 4

> Username: otherjason  
> Created at: 2017-04-12 17:54:43 UTC  
> Url: https://github.com/boostorg/hana/issues/336#issuecomment-293657768  

Every problem can be solved with another layer of indirection. :) Thanks! Should I close this issue? I'm not sure if there's any action for Hana itself here.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2017-04-12 18:12:29 UTC  
> Url: https://github.com/boostorg/hana/issues/336#issuecomment-293662488  

I'm not sure either. @ldionne?

---

## Comment 6

> Username: ldionne  
> Created at: 2017-04-23 19:38:41 UTC  
> Url: https://github.com/boostorg/hana/issues/336#issuecomment-296483136  

Closing. I don't think this is Hana's fault.
