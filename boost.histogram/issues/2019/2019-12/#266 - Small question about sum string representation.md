# #266 - Small question about sum string representation [Closed]

> Username: henryiii  
> Created at: 2019-12-30 16:34:31 UTC  
> Updated at: 2020-01-03 17:08:36 UTC  
> Closed at: 2020-01-03 16:06:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/266  

For the following example program:  
  
```cpp  
#include <iostream>  
#include <boost/histogram.hpp>  
  
int main() {  
    namespace bh = boost::histogram;  
    bh::accumulators::sum sum(1);  
    std::cout << sum << std::endl;  
}  
```  
  
The printout here looks like it should be "`sum(1 + 0)`"  
  
https://github.com/boostorg/histogram/blob/9e5a4c4f2406d84e068dbed7b070baa2c1a38930/include/boost/histogram/accumulators/ostream.hpp#L54-L60  
  
However, it is not, but is just "`1`" ([tested in wandbox](https://wandbox.org/permlink/moRquHpx9B0viUSe) with Boost 1.72). The other accumulators do print as expected, but sum does not, and I don't know why.  
  
If this is the expected behavior, where is it coming from?

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-01-03 15:41:07 UTC  
> Updated at: 2020-01-03 16:02:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/266#issuecomment-570608357  

This is fixed in the develop branch now as part of some other changes. `accumulators::sum` used to be implicitly convertible to its value. You probably forgot to include the ostream.hpp header which has the operator<< defined for `sum<T>`. If it is not visible to the compiler, it tries to do the implicit conversion to get a match against `operator<<(std::ostream&, double)`, which is defined in the iostream headers.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-01-03 15:41:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/266#issuecomment-570608576  

Now `sum<T>` is not implicitly convertible anymore and you will get a compile-time error instead.

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-01-03 16:01:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/266#issuecomment-570614912  

If the header is visible, the ostreamer works also in 1.72, see the test here:  
https://github.com/boostorg/histogram/blob/master/test/accumulators_test.cpp#L174

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-01-03 16:06:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/266#issuecomment-570616341  

Regarding your wandbox example, `#include <boost/histogram.hpp>` does not include the ostream.hpp's, as explained in the documentation. If we would include the ostream.hpp's, the user could not use the master include and write their own ostreamers.

---

## Comment 5

> Username: henryiii  
> Created at: 2020-01-03 17:08:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/266#issuecomment-570634675  

I'm not sure why boost-histogram was using the implicit conversion instead of the header, which was available, but this should avoid ambiguity. Thanks!
