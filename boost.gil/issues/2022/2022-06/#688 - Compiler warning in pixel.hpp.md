# #688 - Compiler warning in pixel.hpp [Open]

> Username: jsenn  
> Created at: 2022-06-17 18:13:44 UTC  
> Updated at: 2022-06-20 14:59:09 UTC  
> Url: https://github.com/boostorg/gil/issues/688  

### Actual behavior  
  
I get the following compiler warnings from pixel.hpp when using some gil functions:  
```  
3>  C:\Colony\VS2015\x64\boost_1_72_0\boost\gil\pixel.hpp(214,31): warning C4244: 'argument': conversion from 'const Channel' to 'BaseChannelValue', possible loss of data  
3>          with  
3>          [  
3>              Channel=int  
3>          ]  
3>          and  
3>          [  
3>              BaseChannelValue=float  
3>          ]  
3>  C:\Colony\VS2015\x64\boost_1_72_0\boost\gil\pixel.hpp(158): note: see reference to function template instantiation 'void boost::gil::pixel<boost::gil::float32_t,boost::gil::gray_layout_t>::assign<Pixel>(const Channel &,std::false_type)' being compiled  
3>          with  
3>          [  
3>              Pixel=int,  
3>              Channel=int  
3>          ]  
3>  C:\Colony\VS2015\x64\boost_1_72_0\boost\gil\pixel.hpp(158): note: see reference to function template instantiation 'void boost::gil::pixel<boost::gil::float32_t,boost::gil::gray_layout_t>::assign<Pixel>(const Channel &,std::false_type)' being compiled  
3>          with  
3>          [  
3>              Pixel=int,  
3>              Channel=int  
3>          ]  
3>  C:\Colony\VS2015\x64\boost_1_72_0\boost\gil\image_processing\numeric.hpp(68): note: see reference to function template instantiation 'boost::gil::pixel<boost::gil::float32_t,boost::gil::gray_layout_t> &boost::gil::pixel<boost::gil::float32_t,boost::gil::gray_layout_t>::operator =<int>(const Pixel &)' being compiled  
3>          with  
3>          [  
3>              Pixel=int  
3>          ]  
3>  C:\Colony\VS2015\x64\boost_1_72_0\boost\gil\image_processing\numeric.hpp(68): note: see reference to function template instantiation 'boost::gil::pixel<boost::gil::float32_t,boost::gil::gray_layout_t> &boost::gil::pixel<boost::gil::float32_t,boost::gil::gray_layout_t>::operator =<int>(const Pixel &)' being compiled  
3>          with  
3>          [  
3>              Pixel=int  
3>          ]  
```  
  
This is in the grayscale pixel assign function, where it's assigning the int channel value directly to the float grayscale value without an explicit cast. Since not every int has a representation as a float, this is a loss of data, hence the warning. I guess the fix would be to add an explicit `static_cast<>` in there like the following?  
```c++  
    template <typename Channel>  
    void assign(Channel const& channel, std::false_type)  
    {  
        check_gray();  
        gil::at_c<0>(*this) = static_cast<channel_t::base_channel_t>( channel );  
    }  
  
  
```  
  
### Expected  behavior  
  
No warning  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <stdexcept>  
#include <boost/gil.hpp>  
#include <boost/gil/io/write_view.hpp>  
#include <boost/gil/extension/io/png.hpp>  
  
int main()  
{  
	int width = 640;  
	int height = 480;  
	unsigned char* pImageData = new unsigned char[width*height*4];  
	boost::gil::rgba8c_view_t imageView = boost::gil::interleaved_view( width, height, ( const boost::gil::rgba8c_pixel_t* )pImageData, width * 4 );  
	try  
	{  
		boost::gil::write_view( "test.png", imageView, boost::gil::png_tag{});  
	}  
	catch ( ... )  
	{  
		return false;  
	}  
}  
```  
  
### Environment  
Compiled in Visual Studio 2019 using MSVC with language standard set to latest (C++20).

---

## Comment 1

> Username: jsenn  
> Created at: 2022-06-17 19:39:27 UTC  
> Url: https://github.com/boostorg/gil/issues/688#issuecomment-1159177200  

After digging into this a bit more, I think the actual problem isn't the lack of a static_cast, but actually just a bug in a part of the code unrelated to my example above. If you look at the warning stack, the root of the problem is [in compute_tensor_entries](https://github.com/boostorg/gil/blob/9bd8642f69bdbf5dfd01df45682efd50dd18fa53/include/boost/gil/image_processing/numeric.hpp#L74), a function in numeric.hpp that's only used in the harris corner example and its associated test.  
  
This code appears to be multiplying 2 16-bit shorts (producing a 32-bit integer), then assigning it to a 32-bit float grayscale pixel. Does this make sense? Shouldn't it be doing some sort of rescaling to make sure the value is between 0 and 1?

---

## Comment 2

> Username: simmplecoder  
> Created at: 2022-06-18 15:42:07 UTC  
> Updated at: 2022-06-18 15:44:54 UTC  
> Url: https://github.com/boostorg/gil/issues/688#issuecomment-1159485877  

@jsenn IIRC the values from Harris corner detector are already small. Either way, the PR from GSoC of 2021 #624 should make it clear that the image used for computations are just treated as matrix of floating point values. There is no implied or required normalization. I'm in a bit of a roaming state atm, but should be able to get the PR in merge-ready state until the end of summer. For now, if you have the time, you could try the example from there and see how it looks like.  
Either way if silencing of warnings is needed, it might be better to make them under separate name, as some people could use warnings as a source of real problems.

---

## Comment 3

> Username: jsenn  
> Created at: 2022-06-20 14:59:09 UTC  
> Url: https://github.com/boostorg/gil/issues/688#issuecomment-1160553680  

Thanks for the quick response @simmplecoder. Is there anything in #624 that would solve the issue with `compute_tensor_entries`? The issue I see is that the output matrices use `boost::gil::float32_t` channel values, which are bounded to the interval [0, 1] (because `float32_t` is defined as a `scoped_channel_value`), but that function is assigning values from the partial derivative images which are given as shorts in the range [0, 65535]. Unless all the derivative values happen to be either 0 or 1, the output matrices will be "overflowed" in the sense that their imposed bounds have been violated. If the input values are greater than 2^12, then their product will be greater than 2^24, at which point there is precision loss from converting an int to a float (which is what the compiler warning is about).  
  
It seems to me (correct me if I'm wrong) that either:  
1. the corner detector should operate entirely on float32 images (in which case no rescaling is required);  
2. there should be a rescale when converting from the 16-bit integer grayscale image to the `float32_t` image;  
3. `compute_tensor_entries` should use a 32-bit integer as the output channel type to avoid precision loss; or  
4. if it's known that the values are below 2^24, it should explicitly cast to float before assigning so the compiler doesn't complain (though this would violate the bounds assumptions of the `float32_t` type).
