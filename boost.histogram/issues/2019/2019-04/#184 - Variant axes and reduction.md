# #184 - Variant axes and reduction [Closed]

> Username: henryiii  
> Created at: 2019-04-23 08:58:30 UTC  
> Updated at: 2019-04-26 08:31:42 UTC  
> Closed at: 2019-04-26 08:12:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/184  

The following code does not work:  
  
```cpp  
std::vector<double> edges {{1.,2.,3.,4.,5.}};  
using ax_vector = std::vector<axis::variant<axis::regular<>, axis::variable<>>>;  
  
auto hist = make_histogram(ax_vector{axis::regular<>(10,0,1), axis::variable<>(edges)});  
auto hist2 = algorithm::reduce(hist, algorithm::shrink(0, .3, .9));  
// Error message:  
// libc++abi.dylib: terminating with uncaught exception of type boost::wrapexcept<boost::bad_get>: boost::bad_get: failed value get using boost::get  
// fish: './test/BoostHistogram-algorithm…' terminated by signal SIGABRT (Abort)  
```  
  
The non-variant version works.  
  
I'm thinking it might be default-constructing the first item in the variant list, rather than matching the original axes?

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-04-23 20:34:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/184#issuecomment-485963698  

I can reproduce the bug, working on it.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-04-26 08:12:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/184#issuecomment-486969332  

Found and fixed the problem. Currently waiting for the CI to test the code, then I will merge locally in my fork and push the changes to boostorg.

---

## Comment 3

> Username: henryiii  
> Created at: 2019-04-26 08:18:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/184#issuecomment-486971081  

Fantastic, thanks!

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-04-26 08:31:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/184#issuecomment-486975009  

Thank you for finding and reporting this bug and the very nice minimal code example!
