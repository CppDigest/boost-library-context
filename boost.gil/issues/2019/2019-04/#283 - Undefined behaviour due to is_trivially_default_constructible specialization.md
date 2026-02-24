# #283 - Undefined behaviour due to is_trivially_default_constructible specialization [Closed]

> Username: mloskot  
> Created at: 2019-04-15 18:40:58 UTC  
> Updated at: 2020-04-17 17:26:58 UTC  
> Closed at: 2019-04-15 21:00:17 UTC  
> Url: https://github.com/boostorg/gil/issues/283  

### Problem  
  
The #274 replaced  
  
```cpp  
namespace boost {  
    template <typename ChannelValue, typename Layout>  
    struct has_trivial_constructor<gil::pixel<ChannelValue,Layout> > : public has_trivial_constructor<ChannelValue> {};  
}  
```  
  
with  
  
https://github.com/boostorg/gil/blob/5611bd58071873d6346fe3cc05fe86c72f697717/include/boost/gil/pixel.hpp#L301-L309  
  
what introduced UB according to:  
  
> C++11 / 20.11.2 Header <type_traits> synopsis  
>    1 The behavior of a program that adds specializations for any  
>   of the class templates defined in this subclause is undefined  unless otherwise specified.  
  
### Solution  
  
We need to replace `std::is_trivially_default_constructible` with new GIL's own trait.  
For example, `gil::detail::has_trivial_constructor`.  
  
### References  
  
The issue was revealed by #282 during compilation with GCC 4.7-4.9 which do not define `std::is_trivially_default_constructible`.  
  
  
/cc @stefanseefeld Thoughts?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-04-15 18:46:21 UTC  
> Url: https://github.com/boostorg/gil/issues/283#issuecomment-483371649  

Why do we need to specialize `struct is_trivially_default_constructible<boost::gil::pixel<ChannelValue, Layout>>` at all ? Why isn't the default DTRT ?  
  
Yes, we can certainly introduce our own version, but it seems to me to be a workaround for a deeper issue.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-04-15 18:49:49 UTC  
> Url: https://github.com/boostorg/gil/issues/283#issuecomment-483372781  

I confess I am reporting the issue as I go and copying output as I compile.  
So, I have not looked into details yet.   
  
Yes, I agree that first, we need to confirm if we need that at all!

---

## Comment 3

> Username: mloskot  
> Created at: 2019-04-15 19:12:47 UTC  
> Url: https://github.com/boostorg/gil/issues/283#issuecomment-483380467  

@stefanseefeld If CI builds are happy about #284, then we should be happy as well :-)
