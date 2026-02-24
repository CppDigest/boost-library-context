# #578 - scanlne_reader for istream not worked [Open]

> Username: fsmoke  
> Created at: 2021-03-15 14:42:13 UTC  
> Updated at: 2021-03-15 14:43:51 UTC  
> Url: https://github.com/boostorg/gil/issues/578  

### Actual behavior  
  
Code not compiles on msvc 16.9.1  
  
see piece of build log below  
```  
1>  d:\libraries\boost_1_75_0\boost\gil\io\get_reader.hpp(160,62): error C2039: 'type': is not a member of 'boost::gil::get_read_device<T,FormatTag,void>'  
1>          with  
1>          [  
1>              T=std::ifstream,  
1>              FormatTag=boost::gil::image_read_settings<boost::gil::jpeg_tag>  
1>          ]  
1>  d:\libraries\boost_1_75_0\boost\gil\io\get_reader.hpp(160): message : see declaration of 'boost::gil::get_read_device<T,FormatTag,void>'  
1>          with  
1>          [  
1>              T=std::ifstream,  
1>              FormatTag=boost::gil::image_read_settings<boost::gil::jpeg_tag>  
1>          ]  
1>  d:\libraries\boost_1_75_0\boost\gil\io\make_scanline_reader.hpp(95): message : see reference to class template instantiation 'boost::gil::get_scanline_reader<Device,boost::gil::image_read_settings<boost::gil::jpeg_tag>>' being compiled  
1>          with  
1>          [  
1>              Device=std::ifstream  
1>          ]  
```  
  
### Expected  behavior  
  
I want use gil for read jpeg line by line from istream  
  
### C++ Minimal  Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <fstream>  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/jpeg.hpp>  
namespace gil = boost::gil;  
int main  
{  
        using tag_t = gil::jpeg_tag;  
	using reader_t = gil::scanline_reader<typename gil::get_read_device<std::istream, tag_t>::type, tag_t>;  
  
	std::ifstream is("d:/test.jpg", std::ios::binary | std::ios::in);  
		  
	reader_t reader = gil::make_scanline_reader(is, tag_t()); //Here an error  
}  
```  
  
### Environment  
  
Inside make_scanline_reader.hpp (line 84) - it seems function recursive:  
  
```  
template <typename Device, typename FormatTag>  
inline  
auto make_scanline_reader(Device& io_dev, FormatTag const&,  
    typename std::enable_if  
    <  
        mp11::mp_and  
        <  
            detail::is_adaptable_input_device<FormatTag, Device>,  
            is_format_tag<FormatTag>  
        >::value  
    >::type* /*dummy*/ = nullptr)  
    -> typename get_scanline_reader<Device, FormatTag>::type  
{  
    return make_scanline_reader(io_dev, image_read_settings<FormatTag>());  
}  
```  
  
- Compiler version: msvc 16.9.1  
- Build settings: windows, 64bit  
- boost 1.75
