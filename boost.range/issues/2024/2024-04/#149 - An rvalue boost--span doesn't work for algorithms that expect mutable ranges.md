# #149 - An rvalue boost::span doesn't work for algorithms that expect mutable ranges [Open]

> Username: jsg72  
> Created at: 2024-04-10 16:00:52 UTC  
> Updated at: 2024-04-10 16:00:52 UTC  
> Url: https://github.com/boostorg/range/issues/149  

An rvalue span does not work for algorithms that modify elements.  In the following example, using an lvalue span works, but using an rvalue span fails to compile.  
  
```  
#include <vector>  
#include <boost/core/span.hpp>  
#include <boost/range/algorithm/fill.hpp>  
  
template <typename T>  
boost::span<T> to_span( std::vector<T>& v ) {  
        return v;  
}  
  
int main() {  
  
   std::vector<unsigned char> vec{ 1, 2, 3, 4, 5 };  
   boost::span<unsigned char> sp{ vec };  
  
   boost::fill( sp, 0 ); // works  
   boost::fill( to_span(vec), 0); // fails  
  
   return 0;  
}  
```  
  
It looks to me that the issue is that `boost::begin` and `boost::end` have overloads for lvalue ranges and const lvalue ranges but not rvalue ranges, so if you pass an rvalue range to an algorithm, it gets treated as a const lvalue range and has const iterators.  That seems to be the right thing to do for containers, but not so much for proxy types like span.
