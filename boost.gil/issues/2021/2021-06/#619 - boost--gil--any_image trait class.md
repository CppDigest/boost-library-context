# #619 - boost::gil::any_image trait class [Closed]

> Username: timocafe  
> Created at: 2021-06-30 20:10:54 UTC  
> Updated at: 2021-07-02 08:05:38 UTC  
> Closed at: 2021-07-02 08:01:59 UTC  
> Url: https://github.com/boostorg/gil/issues/619  

Hello,   
  
I would like have a suggestion to code the following problem. I am reading png images gray8 and rgb8.  
I use the type `any_image<...>` type with the following code:  
  
```cpp  
boost::gil::any_image<boost::gil::gray8_image_t, boost::gil::rgb8_image_t> image;  
boost::gil::read_image(filename, image, boost::gil::png_tag());  
```  
  
This code works perfectly well, however for the rest of my project I need to make conversion to the "associated" float type.  
`boost::gil::gray32f_image_t` and  `boost::gil::rgb32f_image_t`. I mean `gray8_image_t` -> `boost::gil::gray32f_image_t` and `rgb8_image_t ` -> `rgb32f_image_t `.  
  
I have  implemented a very ugly solution associating `std::variant` and `std::visit`.  
  
```cpp  
    // the variant class into any_image is private ...  
    auto make_variant(const boost::gil::any_image<boost::gil::gray8_image_t, boost::gil::rgb8_image_t> &image) {  
        if (image.num_channels() == 1)  
            return image_convert_type(boost::gil::gray32f_image_t(image.dimensions()));  
        else  
            return image_convert_type(boost::gil::rgb32f_image_t(image.dimensions()));  
    }  
```  
  
```cpp  
    boost::gil::any_image<boost::gil::gray8_image_t, boost::gil::rgb8_image_t> image;   
    boost::gil::read_image(filename, image, boost::gil::png_tag());  
    auto va = make_variant(image);  
    std::visit(overloaded{  
                       [&](boost::gil::rgb32f_image_t va) {  
                             // whatever    
                           },  
                        [&](boost::gil::gray32f_image_t va) {  
                             // whatever    
                   },  
               },  
     va);  
```  
  
Does it exist a cleaner way ? I mean get the associated type directly from `any_image` like a trait class ?  
  
Thank you

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-07-01 11:47:04 UTC  
> Url: https://github.com/boostorg/gil/issues/619#issuecomment-872177055  

If you mean getting the type currently hold by the variant, there is no other way than visiting the variant. You might want to use `boost::gil::apply_operation` (or `boost::variant2::visit`) though, rather than converting to `std::variant`:  
  
```cpp  
#include <boost/gil/extension/dynamic_image/any_image.hpp>  
  
int main()  
{  
    boost::gil::any_image<boost::gil::gray8_image_t, boost::gil::rgb8_image_t> image;   
    //boost::gil::read_image(filename, image, boost::gil::png_tag());  
  
    boost::gil::apply_operation(  
        image,  
        [](auto img){  
            // Get the type of the alternative currently held by the variant  
            using image_t = std::decay_t<decltype(img)>;  
  
            // overloaded if fine, but here is anothe way to code that same things  
            if constexpr (std::is_same_v<image_t, boost::gil::rgb8_image_t>) {  
                // Do something specific to rgb8_image_t  
            }  
        }  
    );  
  
    return 0;  
}  
```

---

## Comment 2

> Username: timocafe  
> Created at: 2021-07-01 11:56:26 UTC  
> Url: https://github.com/boostorg/gil/issues/619#issuecomment-872182588  

Indeed I was looking for that (it works). My conversion to the variant was useless. But my meta-programming is a bit rusty and I do not have a full understanding of GIL. Thank you very much.

---

## Comment 3

> Username: mloskot  
> Created at: 2021-07-02 08:05:38 UTC  
> Url: https://github.com/boostorg/gil/issues/619#issuecomment-872804217  

Thanks both. This seems like a good example, hopefully I'll not forget to stick it somewhere in the docs.
