# #257 - Flowless histograms unslicable [Closed]

> Username: henryiii  
> Created at: 2019-12-09 21:27:30 UTC  
> Updated at: 2019-12-20 18:03:48 UTC  
> Closed at: 2019-12-15 17:05:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/257  

You currently cannot slice an axes that has flow bins turned off:  
  
```cpp  
using namespace boost::histogram;  
  
auto h = make_histogram(  
    axis::integer<int, use_default, axis::option::none_t>(0, 10)  
);  
  
auto h1 = algorithm::reduce(h, algorithm::slice(3,6));  
```  
  
```  
terminate called after throwing an instance of 'boost::wrapexcept<std::out_of_range>'  
  what():  at least one index out of bounds  
```  
  
(Seen on wandbox with 1.71 and in Python boost-histogram current develop).

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-12-13 13:16:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/257#issuecomment-565437714  

Huh, that is very strange, thanks for reporting the bug.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-12-15 15:31:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/257#issuecomment-565819695  

Turns out reduce does not work for any axis without underflow/overflow. I am working on a fix.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-12-15 17:05:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/257#issuecomment-565827445  

Fixed in develop

---

## Comment 4

> Username: henryiii  
> Created at: 2019-12-16 15:41:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/257#issuecomment-566115214  

Works, thank you for the fast fix!

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-12-20 18:03:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/257#issuecomment-568022493  

Thank you for reporting this nasty bug. At least it failed instead of giving a wrong result...
