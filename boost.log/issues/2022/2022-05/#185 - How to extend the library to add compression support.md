# #185 - How to extend the library to add compression support [Closed]

> Username: ropieur  
> Created at: 2022-05-16 21:11:34 UTC  
> Updated at: 2022-05-17 14:16:53 UTC  
> Closed at: 2022-05-16 21:44:13 UTC  
> Url: https://github.com/boostorg/log/issues/185  

Hi,  
  
I will need to extend the library to support compression of rotated files. Compression must be performed by the application itself at the rotation time and logrotate is not an option.  
Is there any guideline to follow to extend the library by reusing existing default text backend file collector to support compression of the rotated file? If yes, what should I do to add an additional parameter (like a boolean compressed=true or a compression method like zip, gzip, bz2 ...) in setup file?  
  
Thank you

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-16 21:44:13 UTC  
> Url: https://github.com/boostorg/log/issues/185#issuecomment-1128167994  

File collector itself is not extendable, but you can create a proxy collector that will perform file compression before passing the compressed file to the actual file collector. You will have to implement the [`collector` interface](https://github.com/boostorg/log/blob/dda118619ad4abc0ffb5c603e7e086a291da2c72/include/boost/log/sinks/text_file_backend.hpp#L89-L153), the compression should happen in `store_file` method. Note that your proxy should translate paths and file names as you forward calls to the underlying file collector. There isn't an example in the docs, but it should be simple enough.  
  
As to extending the setup file parser, you may follow [this](https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/extension/settings.html#log.extension.settings.sinks) guide. The sink factory will have to process parameters that are parsed from the settings file and construct and initialize a file sink with your file collector. Note that you don't need to write your own sink backend, only the sink factory.

---

## Comment 2

> Username: ropieur  
> Created at: 2022-05-17 12:58:38 UTC  
> Url: https://github.com/boostorg/log/issues/185#issuecomment-1128837609  

I created a compressor_collector (wrapping a file_collector) as follows:  
```  
struct compression_collector :  
    boost::log::sinks::file::collector,  
    boost::enable_shared_from_this< compression_collector >  
{  
    compression_collector(boost::shared_ptr< boost::log::sinks::file::collector > fileCollector);  
  
    using scan_result = boost::log::sinks::file::scan_result;  
    using scan_method = boost::log::sinks::file::scan_method;  
  
    void store_file(boost::filesystem::path const& src_path) override;  
    bool is_in_storage(boost::filesystem::path const& src_path) const override;  
    scan_result scan_for_files(scan_method method, boost::filesystem::path const& pattern) override;  
  
    boost::shared_ptr< boost::log::sinks::file::collector > fileCollector;  
};  
```  
I would have need to create a text_file_backend with a compression_collector wrapping a file_collector, but as init_from_settings hides completely default_text_file_sink_factory which is also in charge of creating the file_collector based on the settings, this forces me to reimplement all that stuff. Unless you would have another faster solution in mind, wouldn't be possible to open those implementation for reuse?

---

## Comment 3

> Username: Lastique  
> Created at: 2022-05-17 14:16:53 UTC  
> Url: https://github.com/boostorg/log/issues/185#issuecomment-1128928662  

> Unless you would have another faster solution in mind, wouldn't be possible to open those implementation for reuse?  
  
No plans to make it reusable currently. The default sink factories do not allow to hook into sink initialization process, and it's not clear how to implement this.
