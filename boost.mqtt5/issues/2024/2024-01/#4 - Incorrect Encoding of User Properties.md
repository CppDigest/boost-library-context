# #4 - Incorrect Encoding of User Properties [Closed]

> Username: josip-vukusic  
> Created at: 2024-01-23 14:08:23 UTC  
> Updated at: 2024-01-24 12:18:51 UTC  
> Closed at: 2024-01-24 12:18:51 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/4  

Hello,  
  
I noticed a possible small issue in the implementation of the 'encode' function for encoding user properties. The identifier for user properties is added for every string (key and value), but it should be added for pairs of strings ([Section 3.3.2.3](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901054)).  
  
```  
std::string& encode(std::string& s) const {  
    if (_val.empty())  
        return s;  
  
    for (const auto& pr: _val) {  
        auto sval = encoder_for_prop<p>(pr);   
        s.push_back(p);  // should be added for pair of strings not for every string  
        sval.encode(s);  
    }  
    return s;  
}  
```  
[source file](https://github.com/mireo/async-mqtt5/blob/master/include/async_mqtt5/impl/codecs/base_encoders.hpp)

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-01-24 12:18:51 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/4#issuecomment-1908016428  

Hello,  
  
you are correct. We did not encode User Properties correctly.  
We fixed the issue with commit 61f17f6e0f1c9527735ea13f4d23bcb8e8f2787b.  
  
Thank you for pointing this out!
