# #24 - Cannot use boost::multi_index::key with `const& noexcept` member function [Closed]

> Username: phimuemue  
> Created at: 2019-06-05 13:52:56 UTC  
> Updated at: 2019-06-11 15:38:41 UTC  
> Closed at: 2019-06-11 15:38:41 UTC  
> Url: https://github.com/boostorg/multi_index/issues/24  

The following program does not compile (https://wandbox.org/permlink/odsAUNRroBveTxqw). (I tested `clang 8.0.0` and `g++ 9.1.0`.)  
  
```  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/key.hpp>  
  
struct S final {  
    int m_n;  
    S(int n) noexcept : m_n(n) {}  
    int Get() const & noexcept { // removing "& noexcept" makes it compile  
        return m_n;  
    }  
};  
  
int main() {  
    boost::multi_index::multi_index_container<  
        S,  
        boost::multi_index::indexed_by<  
            boost::multi_index::ordered_unique<  
                boost::multi_index::key<&S::Get>  
            >  
        >  
    > container;  
}  
```  
  
`clang 8.0.0` reports:  
  
```  
In file included from prog.cc:3:  
/opt/wandbox/boost-1.70.0/clang-8.0.0/include/boost/multi_index/key.hpp:77:22: error: implicit instantiation of undefined template 'boost::multi_index::detail::typed_key_impl<int (S::*)() const & noexcept, &S::Get, void>'  
struct key_impl<Key>:typed_key_impl<decltype(Key),Key>{};  
                     ^  
/opt/wandbox/boost-1.70.0/clang-8.0.0/include/boost/multi_index/key.hpp:134:23: note: in instantiation of template class 'boost::multi_index::detail::key_impl<&S::Get>' requested here  
  using type=typename key_impl<Keys...>::type;  
                      ^  
/opt/wandbox/boost-1.70.0/clang-8.0.0/include/boost/multi_index/key.hpp:140:1: note: in instantiation of template class 'boost::multi_index::detail::limited_size_key_impl<&S::Get>' requested here  
using key=typename detail::limited_size_key_impl<Keys...>::type;  
^  
prog.cc:18:37: note: in instantiation of template type alias 'key' requested here  
                boost::multi_index::key<&S::Get>  
                                    ^  
/opt/wandbox/boost-1.70.0/clang-8.0.0/include/boost/multi_index/key.hpp:38:8: note: template is declared here  
struct typed_key_impl;  
       ^  
prog.cc:20:9: error: expected a type  
        >  
        ^  
prog.cc:21:5: error: expected a type  
    > container;  
    ^  
prog.cc:21:7: error: C++ requires a type specifier for all declarations  
    > container;  
      ^  
4 errors generated.  
```  
  
I suspect that the problem is that `key_impl` is not specialized for `const&` and/or `noexcept` member functions.

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-06-10 11:50:06 UTC  
> Updated at: 2019-06-10 11:50:22 UTC  
> Url: https://github.com/boostorg/multi_index/issues/24#issuecomment-500389032  

Thank you for your report. I've added support for `volatile`/ref-qualified/`noexcept` memfuns in https://github.com/boostorg/multi_index/commit/e49ae2b4c2c98940a56c81facc08371eb517e108. Could you please test locally if your problem is solved?

---

## Comment 2

> Username: phimuemue  
> Created at: 2019-06-11 12:25:45 UTC  
> Url: https://github.com/boostorg/multi_index/issues/24#issuecomment-500818118  

Thank you. I compiled it locally with Visual Studio, and could not observe any problems, albeit the compiler warns:  
  
```  
key.hpp(90): warning C4003: not enough arguments for function-like macro invocation 'BOOST_MULTI_INDEX_KEY_REMOVE_MEMFUN_NOEXCEPT'`  
```

---

## Comment 3

> Username: joaquintides  
> Created at: 2019-06-11 15:38:41 UTC  
> Url: https://github.com/boostorg/multi_index/issues/24#issuecomment-500898764  

Warning fixed in a7ef250b9f8f0fac66b0ccc3bdef5a3916f89681.  
  
Best regards,
