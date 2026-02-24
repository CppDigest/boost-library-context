# #724 - stream device "tell" method absent! [Closed]

> Username: fsmoke  
> Created at: 2023-02-01 10:31:50 UTC  
> Updated at: 2024-05-17 09:43:56 UTC  
> Closed at: 2024-05-17 09:43:45 UTC  
> Url: https://github.com/boostorg/gil/issues/724  

I am trying write custom io extension - and i need get size to the end of device(stream or file no matter)  
I already wrote all classes and tags for it, but it seems i found bug  
  
i wrote following code to calc size  
  
```  
reader(Device const & device, image_read_settings<my_tag> const & settings) :  
			_io_dev(device), _settings(settings)  
		{  
			//read image   
			auto pos = _io_dev.tell();  
			_io_dev.seek(0, SEEK_END);  
			auto size = _io_dev.tell() - pos;  
			_io_dev.seek(pos, SEEK_SET);  
		}  
```  
following code compiles just fine   
```  
using texture_res_image = boost::gil::any_image<  
	gil::gray8_image_t,  
	gil::rgb8_image_t,  
	gil::rgba8_image_t  
>;  
int main()  
{  
    texture_res_image img;  
    gil::read_image("anton.myext", img, gil::my_tag());  
}  
```  
  
**but i'ev got error when use istream**   
```  
using texture_res_image = boost::gil::any_image<  
	gil::gray8_image_t,  
	gil::rgb8_image_t,  
	gil::rgba8_image_t  
>;  
int main()  
{  
    std::ifstream is("anton.myext", std::ios::in | std::ios::binary);  
    texture_res_image img;  
    gil::read_image(is, img, boost::gil::my_tag());  
}  
```  
  
**error C2039: 'tell': is not a member of 'boost::gil::detail::istream_device<FormatTag>'**  
  
i investigated boost_1_80_0\boost\gil\io\device.hpp - and it's true there is no **tell** method in **istream_device** class :(  
but in file_stream_device it's present.   
  
  
- Compiler version: MSVC 17.4.4  
- Build settings: default  
- Boost 1.80

---

## Comment 1

> Username: fsmoke  
> Created at: 2023-02-01 11:30:35 UTC  
> Updated at: 2023-02-01 11:31:05 UTC  
> Url: https://github.com/boostorg/gil/issues/724#issuecomment-1411909058  

For now i wrote very ugly workaround - it's looks like very very bad!  
  
```  
        namespace detail  
	{  
                //FIXME see BUG https://github.com/boostorg/gil/issues/724  
		template<>  
		class istream_device<jpeg_tag> : public istream_device<void>  
		{  
		public:  
			istream_device(std::istream & in) : istream_device<void>(in), _in(in) {}  
  
			long int tell()  
			{  
				auto pos = _in.tellg();  
  
				io_error_if(pos == -1L, "istream_device: file position error");  
  
				return (long int)pos;  
			}  
		private:  
  
			std::istream & _in;  
		};  
      }  
```  
  
so i'll waiting for your realization  
  
as addition - specialization of devices very unclear - cos it's inside boost::gil::detail - i think it must be as public api

---

## Comment 2

> Username: striezel  
> Created at: 2024-05-17 01:30:17 UTC  
> Updated at: 2024-05-17 01:30:35 UTC  
> Url: https://github.com/boostorg/gil/issues/724#issuecomment-2116467410  

Yes, the `tell()` method is indeed missing in `boost::gil::detail::istream_device`. And in `boost::gil::detail::ostream_device`. And so is the `error()` method, too. I see that this can be problematic in cases like yours.  
  
To fix this, I opened a PR that implements both of those methods for both of the mentioned classes, #747.
