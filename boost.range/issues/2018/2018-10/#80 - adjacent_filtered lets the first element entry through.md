# #80 - adjacent_filtered lets the first element entry through [Open]

> Username: tonyelewis  
> Created at: 2018-10-28 06:42:57 UTC  
> Updated at: 2018-10-28 06:49:00 UTC  
> Url: https://github.com/boostorg/range/issues/80  

Following on from [Trac#13203](https://svn.boost.org/trac10/ticket/13203)&hellip;  
  
`adjacent_filtered` always lets the first element of the range leak through before it starts the real filtering. This means it's easy to get it to violate its stated [postcondition](https://www.boost.org/doc/libs/1_68_0/libs/range/doc/html/range/reference/adaptors/reference/adjacent_filtered.html#range.reference.adaptors.reference.adjacent_filtered):  
  
> For all adjacent elements [x,y] in the returned range, bi_pred(x,y) is true.  
  
Eg:  
  
~~~cpp  
int main() {  
   const std::vector<int> a = { 0, 1, 2, 3, 4, 5 };  
  
   auto b = a | boost::adaptors::adjacent_filtered( [] (const int &x, const int &y) {  
      return ( ( x > 2 ) && ( y > 2 ) );  
   } );  
  
   for (const auto &x : b) {  
      std::cerr << x << "\n";  
   }  
}  
~~~  
  
&hellip;outputs:  
  
~~~no-highlight  
0  
4  
5  
~~~  
  
From what I can see in the code, the predicate is currently only applied in the `increment()` function, which leaves it too late for the first element to be checked.

---

## Comment 1

> Username: tonyelewis  
> Created at: 2018-10-28 06:49:00 UTC  
> Url: https://github.com/boostorg/range/issues/80#issuecomment-433681005  

This is related to #81.
