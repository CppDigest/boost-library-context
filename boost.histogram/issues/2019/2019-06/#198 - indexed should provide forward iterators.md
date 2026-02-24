# #198 - indexed should provide forward iterators [Closed]

> Username: HDembinski  
> Created at: 2019-06-03 19:34:24 UTC  
> Updated at: 2019-06-07 13:25:31 UTC  
> Closed at: 2019-06-07 13:25:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/198  

The iterators provided by `indexed` are input iterators, which means you cannot store them. This prevents the use of many std algorithms, e.g. `std::max_element`. I currently cannot imagine how to provide such iterators without reducing iteration performance, but perhaps it is possible.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-06-03 23:42:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/198#issuecomment-498465370  

Perhaps it is a simpler and easier-to-use alternative to add a method `.index()` to the histogram iterator, which can be used to extract the multidimensional index from the normal storage iterator.  
  
```  
auto h = ...  
auto it = std::max_element(h.begin(), h.end());  
auto index = it.index();  
```  
  
The problem with this approach: the user cannot exclude the *flow bins. For that `indexed` is anyway needed.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-06-04 07:23:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/198#issuecomment-498554040  

Thinking more about it, implementing forward iterators seems like the better option. The accessor should forward all comparison operators to the value, so that `std::max_element` works on accessors as if they were values, simplifying the usage.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-06-07 13:25:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/198#issuecomment-499883626  

implemented in develop
