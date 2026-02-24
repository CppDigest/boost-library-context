# #30 - Unnecessary qualified inheritance causes MSVC ICE [Closed]

> Username: ecatmur  
> Created at: 2018-10-09 14:27:33 UTC  
> Updated at: 2019-03-13 21:46:36 UTC  
> Closed at: 2019-03-13 21:46:35 UTC  
> Url: https://github.com/boostorg/intrusive/issues/30  

In the base-specifier-list of `slist_impl::data_t` the qualification `slist_impl::` is unnecessary: https://github.com/boostorg/intrusive/blob/2ab3421b17009adac5b1f7a688b7962cf4709495/include/boost/intrusive/slist.hpp#L236 This causes an ICE in MSVC https://developercommunity.visualstudio.com/content/problem/352789/c1001-in-compiler-file-msc1cpp-line-1518-with-inhe.html when the following code is compiled:  
```  
#include <boost/intrusive/slist.hpp>  
#include <boost/concept_check.hpp>  
template<class> struct Concept { BOOST_CONCEPT_USAGE(Concept) {} };  
template<class> struct Node : boost::intrusive::slist_base_hook<> {};  
template<class T> struct H {  
    using Y = int;  
    boost::intrusive::slist<Node<T>> x;  
};  
BOOST_CONCEPT_ASSERT((Concept<H<int>::Y>));  
```  
  
Please consider removing this qualification as a workaround. Obviously it will continue to be required within `struct data_t`. https://github.com/boostorg/intrusive/blob/2ab3421b17009adac5b1f7a688b7962cf4709495/include/boost/intrusive/slist.hpp#L238

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-13 21:46:35 UTC  
> Url: https://github.com/boostorg/intrusive/issues/30#issuecomment-472618804  

This bug was solved in commit:  
  
https://github.com/boostorg/intrusive/commit/911774278a5e7a5e2232d187538d1bb564e9dcea#diff-ef05d1b3b42e02b705b124f9a10ea0d3  
  
Many tanks for the report.
