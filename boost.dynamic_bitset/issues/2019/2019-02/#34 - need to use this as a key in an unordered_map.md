# #34 - need to use this as a key in an unordered_map [Closed]

> Username: BrannonKing  
> Created at: 2019-02-04 15:33:46 UTC  
> Updated at: 2019-06-07 17:55:59 UTC  
> Closed at: 2019-06-07 17:55:59 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34  

I need the ability to cache computations made from various bitsets. However, dynamic_bitset is not usable as a key right now as std::hash does not support it. Please add support for this.

---

## Comment 1

> Username: jeking3  
> Created at: 2019-02-21 16:45:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-466073621  

@BrannonKing please note this library is maintained by the Boost Community Maintenance Team - the fastest resolution for your issue is to submit a pull request with new code.  What you suggested sounds like an interesting idea.

---

## Comment 2

> Username: EHadoux  
> Created at: 2019-06-06 10:03:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499432357  

This one here (#1) has done it 5 years ago but weirdly enough it has never been merged.

---

## Comment 3

> Username: EHadoux  
> Created at: 2019-06-06 10:29:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499440521  

@BrannonKing In the meantime you can use something like:  
```C++  
#include <boost/dynamic_bitset.hpp>  
#include <boost/functional/hash.hpp>  
namespace std {  
  template<> struct hash<boost::dynamic_bitset<>> {  
    std::size_t operator()(const boost::dynamic_bitset<>& bs) const {  
      std::string h;  
      boost::to_string(bs, h);  
      return std::hash<std::string>{}(h);  
    }  
  };  
}  
```  
And include that before everything else. Bear in mind though that for instance 00100 and 100 won't have the same hash. But if I'm not mistaken they aren't equal anyway (which, I think, is annoying).

---

## Comment 4

> Username: jeking3  
> Created at: 2019-06-06 11:32:19 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499457630  

`00100` and `100` are two different bitsets, why would they be equal?

---

## Comment 5

> Username: EHadoux  
> Created at: 2019-06-06 13:40:14 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499497922  

Are they really though? There is no point in arguing on that as it won't be changed but if you consider a bitset as an ordered set of bits, I don't think they should.   
At least two of the main uses of bitsets (representing a number by its bits and the existence/appartenance of indexed objects) fall under the closed-world assumption.   
```00100``` and ```100``` both represent the same number and item number 4  is inexistant/absent in both as well. For the latter use we can argue that one represents the existence of 5 objects and the other of 3 but without all the preconditions it would work the same if ```test(4)``` was ```false``` in both cases (instead of ```false``` and hitting the precondition).  
Anyway, it doesn't really matter now that it has been done like that for ages.

---

## Comment 6

> Username: jeking3  
> Created at: 2019-06-06 19:10:53 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499628608  

You are making a lot of dangerous assumptions:  
  
1. Assuming the overall length is meaningless.  
2. Assuming the consumer is going to convert the content to integers / leading zeroes are ignored.  
  
What if this dynamic_bitset is storing free disk sectors?  In one case, sector 2 is free, and in the other case, sector 0 is free.  How could these possibly be equal?

---

## Comment 7

> Username: EHadoux  
> Created at: 2019-06-06 21:23:51 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499673046  

I probably wasn’t clear but sequences of bits have to be read from right to left. In both cases it’s a 4.

---

## Comment 8

> Username: jeking3  
> Created at: 2019-06-07 09:38:00 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/34#issuecomment-499822819  

Even in that case the dynamic_bitset size() may be used to store important information like the total number of sectors (in my example) and therefore I would argue they are still not equal, and therefore should have different hash values.
