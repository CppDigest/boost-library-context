# #47 - Review the Constantness of comparing Pairs [Closed]

> Username: ldionne  
> Created at: 2015-04-17 20:07:05 UTC  
> Updated at: 2015-08-25 16:34:23 UTC  
> Closed at: 2015-08-25 16:34:23 UTC  
> Url: https://github.com/boostorg/hana/issues/47  

The following comparison does not return a `Constant`:  
  
``` cpp  
#include <boost/hana/pair.hpp>  
  
struct invalid { };  
  
int main() {  
    hana::make_pair('x', invalid{}) == hana::make_pair('x', 2);  
}  
```  
  
One could argue that it should, because `invalid{} == 2` returns `false_`. Fixing this for `Pair` should be fairly easy, but it raises the issue that `Tuple` should do the same, which is probably harder to implement.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-25 16:34:13 UTC  
> Url: https://github.com/boostorg/hana/issues/47#issuecomment-134663993  

I suspect it is impossible to implement this in a general way (e.g. for `Tuple`). Consider the following:  
  
``` c++  
struct error { };  
  
auto xs = make_tuple(1, error{}, int_<2>);  
auto ys = make_tuple(1, error{}, int_<3>);  
auto result = equal(xs, ys);  
```  
  
What should `result` be? As humans, we can determine that it should be `false_`, because `int_<2> == int_<3>` is `false_`. However, even if we tried to perform all the comparisons that return `Constant`s before the other comparisons, we would fail when doing `equal(error{}, error{})`, because `error` is not `Comparable`. Of course, we might be able to skip those comparisons that would trigger an error on a first pass, but that's starting to be very complicated.  
  
I'm also unsure about the value of conserving the constant-ness of the comparison in such corner cases, so I'll just close this and reopen if there is a real need for it.
