# #319 - boost::hana::type definition does not allow type inference [Closed]

> Username: davidstone  
> Created at: 2017-02-05 21:10:23 UTC  
> Updated at: 2017-02-05 22:21:49 UTC  
> Closed at: 2017-02-05 22:21:49 UTC  
> Url: https://github.com/boostorg/hana/issues/319  

#include <boost/hana.hpp>  
#include <type_traits>  
  
template<typename T>  
constexpr void f(boost::hana::type<T>) {  
}  
  
int main() {  
	f(boost::hana::type_c<int>);  
}  
  
  
Compiling this with clang gives me the error message:  
  
source/main.cpp:9:2: error: no matching function for call to 'f'  
        f(boost::hana::type_c<int>);  
        ^  
source/main.cpp:5:16: note: candidate template ignored: couldn't infer template argument 'T'  
constexpr void f(boost::hana::type<T>) {  
               ^  
1 error generated.  
  
  
I want to do this instead of making the entire function parameter a template parameter because I want to support the user passing in either a type or an index, so I am trying to overload a function with something like std::integral_constant and boost::hana::type.  
  
Obviously I could work around this with enable_if, but that seems to defeat the purpose. I would expect to be able to overload on boost::hana::type parameters like I can with most other "normal" types.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-02-05 21:13:29 UTC  
> Updated at: 2017-02-05 21:13:52 UTC  
> Url: https://github.com/boostorg/hana/issues/319#issuecomment-277549897  

From [the documentation](http://boostorg.github.io/hana/structboost_1_1hana_1_1type.html):  
  
> For subtle reasons having to do with ADL, the actual representation of `hana::type` is implementation-defined. In particular, `hana::type` may be a dependent type, so one should not attempt to do pattern matching on it. However, one can assume that `hana::type` inherits from `hana::basic_type`, which can be useful when declaring overloaded functions:  
> ```c++  
> template <typename T>  
> void f(hana::basic_type<T>) {  
>     // do something with T  
> }  
> ```  
  
Does that solve your problem?

---

## Comment 2

> Username: ldionne  
> Created at: 2017-02-05 21:15:19 UTC  
> Url: https://github.com/boostorg/hana/issues/319#issuecomment-277550047  

Note that if I were to redesign this, I would instead make `hana::type` as straightforward as possible, and then I'd have something like `hana::protect` to work around this ADL issue, which is basically never relevant.

---

## Comment 3

> Username: davidstone  
> Created at: 2017-02-05 21:26:11 UTC  
> Url: https://github.com/boostorg/hana/issues/319#issuecomment-277550787  

This allows me to work around the issue.  
  
It seems like users will almost always want to use boost::hana::basic_type, and the documentation does not seem to explain when I would want to use boost::hana::type instead.  
  
Also, how would I get to those documentation pages from http://www.boost.org/doc/libs/1_63_0/libs/hana/doc/html/index.html ?

---

## Comment 4

> Username: ldionne  
> Created at: 2017-02-05 21:43:31 UTC  
> Url: https://github.com/boostorg/hana/issues/319#issuecomment-277552072  

> It seems like users will almost always want to use `boost::hana::basic_type`  
  
Yes, except when you want to prevent ADL from instantiating the `T` inside `hana::type<T>`.  
  
> and the documentation does not seem to explain when I would want to use `boost::hana::type` instead.  
  
That's fair. I updated the note in the documentation to explain why `hana::type` is implemented the way it is. See https://github.com/boostorg/hana/commit/323b657bbe8841e0fdb42ac07d4a9b432b6f0450.  
  
> Also, how would I get to those documentation pages from http://www.boost.org/doc/libs/1_63_0/libs/hana/doc/html/index.html?  
  
From the left hand side menu, select `Reference documentation > Data types > type`.
