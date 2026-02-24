# #296 - Getting 'no match for call to' with remove_if [Closed]

> Username: jplatte  
> Created at: 2016-07-20 00:10:10 UTC  
> Updated at: 2016-07-20 00:49:33 UTC  
> Closed at: 2016-07-20 00:34:27 UTC  
> Url: https://github.com/boostorg/hana/issues/296  

This is probably an issue in my own code, but my compiler isn't helping me a lot in finding out why it doesn't work. Here is a small test case using the is_pointer function from the user guide:  
  
``` c++  
#include <boost/hana.hpp>  
  
using namespace boost::hana;  
  
template <typename T>  
constexpr auto is_pointer(basic_type<T> const&)  
{ return bool_c<false>; }  
  
template <typename T>  
constexpr auto is_pointer(basic_type<T*> const&)  
{ return bool_c<true>; }  
  
int main()  
{  
    auto xs = make_tuple(type_c<int>, type_c<int*>);  
    auto ys = remove_if(xs, is_pointer);  
}  
```  
  
Full error message from g++ 6.1.1:  
  
```  
test3.cpp: In function 'int main()':  
test3.cpp:16:39: error: no match for call to '(const boost::hana::remove_if_t) (boost::hana::tuple<boost::hana::type_impl<int>::_, boost::hana::type_impl<int*>::_>&, <unresolved overloaded function type>)'  
     auto ys = remove_if(xs, is_pointer);  
                                       ^  
In file included from hana/include/boost/hana/remove_if.hpp:13:0,  
                 from hana/include/boost/hana/map.hpp:50,  
                 from hana/include/boost/hana.hpp:133,  
                 from test3.cpp:1:  
hana/include/boost/hana/fwd/remove_if.hpp:62:24: note: candidate: template<class Xs, class Pred> constexpr auto boost::hana::remove_if_t::operator()(Xs&&, Pred&&) const  
         constexpr auto operator()(Xs&& xs, Pred&& pred) const;  
                        ^~~~~~~~  
hana/include/boost/hana/fwd/remove_if.hpp:62:24: note:   template argument deduction/substitution failed:  
test3.cpp:16:39: note:   couldn't deduce template parameter 'Pred'  
     auto ys = remove_if(xs, is_pointer);  
                                       ^  
```  
  
Full error message from clang 3.8.0:  
  
```  
test3.cpp:16:15: error: no matching function for call to object of type 'const boost::hana::remove_if_t'  
    auto ys = remove_if(xs, is_pointer);  
              ^~~~~~~~~  
hana/include/boost/hana/remove_if.hpp:26:33: note: candidate template ignored: couldn't infer template argument 'Pred'  
    constexpr auto remove_if_t::operator()(Xs&& xs, Pred&& pred) const {  
                                ^  
1 error generated.  
```  
  
It is possible to pass generic lambdas to functions to have them instantiated with different types inside the function. Is the same not true for template functions or am I doing something wrong?  
  
EDIT: I guess I answered my own question by trying out `remove_if(xs, [](auto x) { return is_pointer(x); })` instead of `remove_if(xs, is_pointer)`, which works. It doesn't feel good, but I'll probably do that then. Feel free to close this issue if this is there is no better solution.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-07-20 00:23:47 UTC  
> Updated at: 2016-07-20 00:25:39 UTC  
> Url: https://github.com/boostorg/hana/issues/296#issuecomment-233805400  

Your `is_pointer` needs to be an instance of a function object. Using `hana::traits::is_pointer` is even better. See below:  
  
``` cpp  
#include <boost/hana.hpp>  
  
using namespace boost::hana;  
  
struct is_pointer_fn  
{  
    template <typename T>  
    constexpr auto operator()(basic_type<T> const&) const  
    { return false_c; }  
  
    template <typename T>  
    constexpr auto operator()(basic_type<T*> const&) const  
    { return true_c; }  
};  
  
constexpr is_pointer_fn is_pointer{};  
  
int main()  
{  
    auto xs = make_tuple(type_c<int>, type_c<int*>);  
    auto ys = remove_if(xs, is_pointer);  
  
    // or this  
    auto zs = remove_if(xs, traits::is_pointer);  
}  
```  
  
Also note that you can lift your own metafunctions with `hana::trait`, which is more compile-time friendly than using function overloading.  
http://www.boost.org/doc/libs/1_61_0/libs/hana/doc/html/group__group-Metafunction.html#ga6d4093318f46472e62f9539a4dc998a9

---

## Comment 2

> Username: jplatte  
> Created at: 2016-07-20 00:34:27 UTC  
> Updated at: 2016-07-20 00:35:29 UTC  
> Url: https://github.com/boostorg/hana/issues/296#issuecomment-233806899  

I only used `is_pointer` in my repro, in my actual code I have a few functions the pattern match on some template types of my own. I don't need to lift existing metafunctions as they are part of my own library and I can write them as function objects to begin with now. Thanks for that suggestion, I don't know why I didn't think about that (maybe because I never saw a function object with multiple `operator()`s before). That seems better than wrapping the function in a generic lambda everywhere I want to pass it to another function :)

---

## Comment 3

> Username: ldionne  
> Created at: 2016-07-20 00:44:45 UTC  
> Url: https://github.com/boostorg/hana/issues/296#issuecomment-233808196  

Note that this is actually an issue with the C++ language, not Hana. C++ won't allow you to pass an overload set (an overloaded function) to an higher-order algorithm. [P0119R2](http://open-std.org/JTC1/SC22/WG21/docs/papers/2016/p0119r2.pdf) by Andrew Sutton is a proposal to change this.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-07-20 00:49:33 UTC  
> Url: https://github.com/boostorg/hana/issues/296#issuecomment-233808867  

Also, as another side note, with Fit you can use [`FIT_LIFT`](http://fit.readthedocs.io/en/latest/include/fit/lift.html) to pass the overload set into the function, however, on pre-c++17 it won't preserve constexpr.  
  
``` cpp  
template <typename T>  
constexpr auto is_pointer(basic_type<T> const&)  
{ return bool_c<false>; }  
  
template <typename T>  
constexpr auto is_pointer(basic_type<T*> const&)  
{ return bool_c<true>; }  
  
int main()  
{  
    auto xs = make_tuple(type_c<int>, type_c<int*>);  
    auto ys = remove_if(xs, FIT_LIFT(is_pointer));  
}  
```  
  
Hopefully with Andrew's proposal a macro will no longer be needed.
