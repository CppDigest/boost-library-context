# #204 - Channel arithmetics [Open]

> Username: sdebionne  
> Created at: 2018-12-19 08:32:17 UTC  
> Updated at: 2019-08-01 09:24:57 UTC  
> Url: https://github.com/boostorg/gil/issues/204  

Not really an issue but a following up of the discussion in #191.  
  
# Type conversions / promotions  
  
Let's consider for instance the current implementation of `channel_divides_t`:  
  
```c++  
template <typename Channel1,typename Channel2,typename ChannelR>  
struct channel_divides_t {  
    ChannelR operator()(typename channel_traits<Channel1>::const_reference ch1,  
                        typename channel_traits<Channel2>::const_reference ch2) const {  
        return ChannelR(ch1)/ChannelR(ch2);  
    }  
};  
```  
  
The current casting / promotion policy of the channel values is "explicit conversion of operands", but this is problematic for some use cases. Given `ChannelR = int`:  
  
- `int{128} / int{0.8};` (current implementation) gives div by zero.  
- `int{128 / d0.8};` gives the expected result.  
  
# Optimization  
  
The current implementation comes with the following warning:  
  
```c++  
/// this is a generic implementation; user should specialize it for better performance  
```  
  
Does anyone knows what the original authors had in mind?  
  
# Pixel arithmetic operators  
  
I wonder if we could offer pixel arithmetic with operators, something like this:  
  
```c++  
template <typename Pixel>  
pixel& operator+(const Pixel& rhs)  
{  
    static_transform(*this, rhs, pixel_plus_t<pixel, Pixel, pixel>());  
    return *this;  
}  
```  
  
Expression templates could be used to remove the temporary values but that may be overkill.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-01-11 09:41:50 UTC  
> Url: https://github.com/boostorg/gil/issues/204#issuecomment-453453992  

> /// this is a generic implementation; user should specialize it for better performance  
> Does anyone knows what the original authors had in mind?  
  
This seems like a very old comment. I have no idea really.  
  
> The current casting / promotion policy of the channel values is "explicit conversion of operands", but this is problematic for some use case  
  
Yes, it needs to be improved. I believe, it will also improve quality in terms of solving numerous warnings.  
  
> I wonder if we could offer pixel arithmetic with operators  
  
I think, it is an idea worth to consider.  
  
What is implementation of `pixel_plus`, `pixel_divides` to solve the problematic casting/promotion?  
  
-----  
  
IMO, `pixel_plus_t`  should read `pixel_plus`, without `_t` suffix needed. I assume, they are modelled/named after the standard operator wrappers.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-08-01 09:24:57 UTC  
> Url: https://github.com/boostorg/gil/issues/204#issuecomment-517204444  

https://github.com/boostorg/gil/issues/362#issuecomment-517203864 discusses the idea of `gil::select_most_precise` borrowed from Boost.Geometry.
