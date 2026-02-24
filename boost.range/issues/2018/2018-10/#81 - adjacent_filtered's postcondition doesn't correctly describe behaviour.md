# #81 - adjacent_filtered's postcondition doesn't correctly describe behaviour [Open]

> Username: tonyelewis  
> Created at: 2018-10-28 06:48:38 UTC  
> Updated at: 2018-10-28 06:48:55 UTC  
> Url: https://github.com/boostorg/range/issues/81  

I think the documentation for `adjacent_filtered`'s [postcondition](https://www.boost.org/doc/libs/1_68_0/libs/range/doc/html/range/reference/adaptors/reference/adjacent_filtered.html#range.reference.adaptors.reference.adjacent_filtered), ie:  
  
> For all adjacent elements `[x,y]` in the returned range, `bi_pred(x,y)` is `true`.  
  
&hellip;isn't quite right. For example, this:  
  
~~~cpp  
#include <boost/range/adaptor/adjacent_filtered.hpp>  
  
#include <iostream>  
#include <vector>  
  
int main() {  
   const std::vector<int> a = { 0, 1, 2, 3, 4, 5, 6 };  
  
   const auto b = a | boost::adaptors::adjacent_filtered(  
      [] (const int &x, const int &y) {  
         return ( y % 2 == 1 ) && ( y == x + 1 );  
      }  
   );  
   for (const auto &x : b) {  
      std::cerr << x << "\n";  
   }  
}  
~~~  
  
…outputs:  
  
~~~no-highlight  
0  
1  
3  
5  
~~~  
  
Yet two of the pairs of adjacent elements `[x,y]` in this range fail `bi_pred(x,y)` (because they differ by 2, not 1).  
  
I think it's more like: `bi_pred` is true on each element in the returned range preceded by the element that preceded it in the original range (not in the returned range).

---

## Comment 1

> Username: tonyelewis  
> Created at: 2018-10-28 06:48:55 UTC  
> Url: https://github.com/boostorg/range/issues/81#issuecomment-433681001  

This is related to #80.
