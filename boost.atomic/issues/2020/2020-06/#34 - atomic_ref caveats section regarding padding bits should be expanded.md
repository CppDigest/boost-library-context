# #34 - atomic_ref caveats section regarding padding bits should be expanded [Closed]

> Username: AlexGuteniev  
> Created at: 2020-06-07 13:40:17 UTC  
> Updated at: 2020-06-07 17:55:13 UTC  
> Closed at: 2020-06-07 14:28:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/34  

Consider the following examples:  
```  
#include <boost/atomic/atomic_ref.hpp>  
  
int main()  
{  
    struct FourBits  
    {  
        char b : 4;  
    };  
  
    alignas(boost::atomic_ref<FourBits>::required_alignment) FourBits b1;  
    FourBits b2, b3;  
  
    memcpy(&b1, "\xF1", 1);  
    memcpy(&b2, "\xA1", 1);  
    memcpy(&b3, "\x52", 1);  
  
    assert(b1.b == 1);  
    assert(b2.b == 1);  
    assert(b3.b == 2);  
  
    boost::atomic_ref<FourBits>(b1).compare_exchange_strong(b2, b3);  
  
    assert(b1.b == 2); // this fails  
}  
```  
and:  
```  
int main()  
{  
    struct FourBytes  
    {  
        short a;  
        char  b;  
    };  
  
    alignas(boost::atomic_ref<FourBytes>::required_alignment) FourBytes b1;  
    FourBytes b2, b3;  
  
    memcpy(&b1, "\x00\x00\x01\xF0", sizeof(FourBytes));  
    memcpy(&b2, "\x00\x00\x01\xA0", sizeof(FourBytes));  
    memcpy(&b3, "\x00\x00\x02\x50", sizeof(FourBytes));  
  
    assert(b1.b == 1);  
    assert(b2.b == 1);  
    assert(b3.b == 2);  
  
    boost::atomic_ref<FourBytes>(b1).compare_exchange_strong(b2, b3);  
  
    assert(b1.b == 2); // this fails  
}  
```  
  
Both fail, but I believe according to http://wg21.link/p1123r0 should succeed.  
  
I don't think it can be done without compiler support, or too much overhead, so probably **caveats** documentation should be expanded.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-06-07 14:28:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/34#issuecomment-640227193  

Duplicates https://github.com/boostorg/atomic/issues/35. The current docs already mention that types with padding bits are not supported ([here](https://www.boost.org/doc/libs/1_73_0/doc/html/atomic/interface.html#atomic.interface.interface_atomic_object) and [here](https://www.boost.org/doc/libs/1_73_0/doc/html/atomic/interface.html#atomic.interface.interface_atomic_ref.caveats)).

---

## Comment 2

> Username: AlexGuteniev  
> Created at: 2020-06-07 14:42:01 UTC  
> Url: https://github.com/boostorg/atomic/issues/34#issuecomment-640229225  

Probably this could be a bit more noticeable in the documentation. Starting C++20, it is not just a caveat, it is nonconforming behavior.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-06-07 17:50:40 UTC  
> Url: https://github.com/boostorg/atomic/issues/34#issuecomment-640255037  

I've updated the docs.

---

## Comment 4

> Username: AlexGuteniev  
> Created at: 2020-06-07 17:55:13 UTC  
> Url: https://github.com/boostorg/atomic/issues/34#issuecomment-640255619  

Great, thanks!
