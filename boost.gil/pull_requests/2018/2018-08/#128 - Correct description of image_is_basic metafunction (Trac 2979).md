# #128 Correct description of image_is_basic metafunction (Trac 2979) [Merged]

> Username: mloskot  
> Created at: 2018-08-23 09:28:14 UTC  
> Updated at: 2018-10-12 15:48:55 UTC  
> Merged at: 2018-09-24 18:29:37 UTC  
> Closed at: 2018-09-24 18:29:37 UTC  
> Url: https://github.com/boostorg/gil/pull/128  

https://svn.boost.org/trac10/ticket/2979 description:  
  
> The description of the metafunction boost::gil::image_is_basic says that  
> it will return mpl::true_ if the image uses a basic view and  
> std::allocator<unsigned char>, however the implementation returns true  
> for any kind images that use any kind of allocator.  
  
### Tasklist  
  
- [x] Review (@chhenning & @stefanseefeld could you review the change, please?)  
- [x] Adjust for comments

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-08-26 00:01:02 UTC  
> Url: https://github.com/boostorg/gil/pull/128#issuecomment-416004197  

Are we using image_is_basic anywhere?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-08-26 10:02:32 UTC  
> Url: https://github.com/boostorg/gil/pull/128#issuecomment-416027281  

https://github.com/boostorg/gil/blob/ed67f9a57152624e126d6086f3aa4e6b0f097196/include/boost/gil/extension/dynamic_image/reduce.hpp#L478-L479  
  
Hopefully, there are no users who rely on it.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-09-24 18:25:19 UTC  
> Url: https://github.com/boostorg/gil/pull/128#issuecomment-424076123  

@chhenning, @stefanseefeld any final comments on this one, before I hit the Merge button?

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-09-24 18:29:47 UTC  
> Url: https://github.com/boostorg/gil/pull/128#issuecomment-424077491  

@chhenning Thanks

---
