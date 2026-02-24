# #31 - Support for boost serialization. [Closed]

> Username: venkat-murty  
> Created at: 2025-06-19 05:44:47 UTC  
> Updated at: 2025-06-20 10:10:08 UTC  
> Closed at: 2025-06-19 17:52:05 UTC  
> Url: https://github.com/boostorg/bloom/issues/31  

Can bloom filter support boost::serialization?

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-19 17:48:07 UTC  
> Updated at: 2025-06-20 10:10:08 UTC  
> Url: https://github.com/boostorg/bloom/issues/31#issuecomment-2988798887  

Currently, the library does not support Boost.Serialization natively, but it's very easy to do the integration yourself:  
```cpp  
#include <boost/bloom.hpp>  
#include <boost/serialization/split_member.hpp>  
#include <boost/serialization/nvp.hpp>  
  
struct my_class  
{  
  boost::bloom::filter<int, 10> f{10000};  
  
private:  
  friend class boost::serialization::access;  
  BOOST_SERIALIZATION_SPLIT_MEMBER()  
  
  template<class Archive>  
  void save(Archive& ar, unsigned int) const  
  {  
    const std::size_t c = f.capacity();  
    ar << boost::serialization::make_nvp("capacity", c);  
    auto a = f.array();  
    ar.save_binary(a.data(), a.size());  
  }  
  
  template<class Archive>  
  void load(Archive& ar, unsigned int)  
  {  
    std::size_t c;  
    ar >> boost::serialization::make_nvp("capacity", c);  
    f.reset(c);  
    auto a = f.array();  
    ar.load_binary(a.data(), a.size());  
  }  
};  
```
