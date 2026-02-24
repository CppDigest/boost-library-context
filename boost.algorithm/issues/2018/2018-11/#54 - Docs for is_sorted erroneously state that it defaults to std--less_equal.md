# #54 - Docs for is_sorted erroneously state that it defaults to std::less_equal [Closed]

> Username: tonyelewis  
> Created at: 2018-11-01 21:07:54 UTC  
> Updated at: 2018-11-02 13:15:18 UTC  
> Closed at: 2018-11-02 00:45:59 UTC  
> Url: https://github.com/boostorg/algorithm/issues/54  

<sub><sup>Following on from [Trac#13383](https://svn.boost.org/trac10/ticket/13383)&hellip;</sup></sub>  
  
[Docs](https://www.boost.org/doc/libs/1_68_0/libs/algorithm/doc/html/the_boost_algorithm_library/CXX11/is_sorted.html#the_boost_algorithm_library.CXX11.is_sorted.is_sorted) for `is_sorted()` currently say:  
  
> If no comparison predicate is specified, then std::less_equal is used (i.e, the test is to see if the sequence is non-decreasing)  
  
&hellip;but that's not true&mdash;it defaults to `std::less`&mdash;see [line 60 of `boost/algorithm/cxx11/is_sorted.hpp`](https://github.com/boostorg/algorithm/blob/b6c04d6dc52ace864bb1f5cd26b65f9b639a39b9/include/boost/algorithm/cxx11/is_sorted.hpp#L60).  
  
This code confirms the default behaviour matches `std::less` not `std::less_equal`.  
  
~~~cpp  
int main () {  
  std::vector<int> a        = { 1, 2, 2, 3 };  
  
  std::cerr << "default   : " << std::boolalpha << boost::algorithm::is_sorted( a                      ) << "\n";  
  std::cerr << "less      : " << std::boolalpha << boost::algorithm::is_sorted( a, std::less      <>{} ) << "\n";  
  std::cerr << "less_equal: " << std::boolalpha << boost::algorithm::is_sorted( a, std::less_equal<>{} ) << "\n";  
}  
~~~  
  
I vote: change the docs, not the code, because the code matches `std::`. I'm happy to submit a PR for this - please shout if I should do that.

---

## Comment 1

> Username: mclow  
> Created at: 2018-11-01 23:15:56 UTC  
> Url: https://github.com/boostorg/algorithm/issues/54#issuecomment-435218276  

I'll fix the docs.

---

## Comment 2

> Username: tonyelewis  
> Created at: 2018-11-01 23:19:04 UTC  
> Url: https://github.com/boostorg/algorithm/issues/54#issuecomment-435218885  

Thanks.

---

## Comment 3

> Username: mclow  
> Created at: 2018-11-02 00:45:59 UTC  
> Url: https://github.com/boostorg/algorithm/issues/54#issuecomment-435234661  

Fixed in develop 1cbe285.

---

## Comment 4

> Username: tonyelewis  
> Created at: 2018-11-02 13:15:17 UTC  
> Url: https://github.com/boostorg/algorithm/issues/54#issuecomment-435376577  

Great. Thanks very much.
