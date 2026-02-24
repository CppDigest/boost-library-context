# #579 - for_each_pixel not work for any_image [Closed]

> Username: fsmoke  
> Created at: 2021-03-16 08:37:57 UTC  
> Updated at: 2022-04-30 21:57:01 UTC  
> Closed at: 2022-04-30 21:57:01 UTC  
> Url: https://github.com/boostorg/gil/issues/579  

I suggest solution with boost::variant2::visit, see code below(code is approximated, not tested and etc, writed right here - it's just idea) you can write specialization for for_each_pixel...i think  
  
```cpp  
template <typename View, typename F,/*may be add sfinae here*/>  
F for_each_pixel(View const & view, F fun)  
{  
	return boost::variant2::visit([&fun](auto && v)  
		{  
			return for_each_pixel(v, fun);  
		}, view);  
}  
```  
example of usage   
```cpp  
using tag_t = gil::jpeg_tag;  
using res_image = gil::any_image<  
	gil::gray8_image_t,  
	gil::rgb8_image_t,  
	gil::bgr8_image_t,  
	gil::rgba8_image_t,  
	gil::abgr8_image_t,  
	gil::argb8_image_t>;  
  
res_image img;  
gil::read_image("blabla.jpg", img, tag_t());  
  
gil::for_each_pixel(gil::const_view(img), [](auto & pixel))  
{  
      using TT = std::decay_t<decltype(pixel)>;  
      //do here what you want with pixel  
});  
```

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-03-16 13:08:57 UTC  
> Url: https://github.com/boostorg/gil/issues/579#issuecomment-800244000  

Thanks for the suggestion. No need for SFINAE here, just overload the algorithm:  
```c++  
template <typename ...Types, typename F>  
F for_each_pixel(any_image_view<Types...> const& src, F fun)  
{  
    return apply_operation(src, [&fun](auto && v) {  
        return for_each_pixel(v, fun);  
    });  
}  
```
