# #94 - Manual close of code_converter device does not flush data [Open]

> Username: mvyskoc  
> Created at: 2019-05-05 11:02:34 UTC  
> Updated at: 2019-05-05 11:02:34 UTC  
> Url: https://github.com/boostorg/iostreams/issues/94  

Only when you manualy close `boost::iostream::code_converter` device the data are not flushed into the mapped device.  
  
When you execute following fragment of the code, there is created empty file `log.txt`.   
```CPP  
void write_text_file()  
{  
  
    boost::locale::generator gen;  
    typedef io::code_converter<io::file_descriptor_sink> converter_device;  
    typedef io::stream<converter_device> converter_stream;  
  
    io::file_descriptor_sink file_sink;  
    converter_device dev;  
    converter_stream stream;  
  
    dev.imbue(gen("C.UTF-8"));  
  
    file_sink.open("log.txt", std::ios_base::out);  
  
    dev.open(file_sink);  
    stream.open(dev);  
  
    stream << L"Write UTF-8 string\n"  
           << L"Zapiš UTF-8 řetězec";  
  
    stream.close();  
    dev.close();  
    file_sink.close();  
      
    cout << "file_sink.is_open = " << file_sink.is_open() << "\n"  
         << "dev.is_open = " << dev.is_open() << "\n"  
         << "stream.is_open = " << stream.is_open();  
}  
```  
  
From the following output of the program is see the converter device is still open:  
```  
file_sink.is_open = 0  
dev.is_open = 1  
stream.is_open = 0  
```  
The reason is the method `close` has the input argument `which` for identification to close input or output stream. The default value is bitwise or of  constant `in | out`.  
  
```CPP  
class code_converter   
    : protected code_converter_base<Device, Codecvt, Alloc>  
...  
public:  
    void close(BOOST_IOS::openmode which = BOOST_IOS::in | BOOST_IOS::out )  
    { impl().close(which); }  
```  
  
Private method `impl()` returns instance of `code_converter_impl` where the method `close` is overladed and implemented as follows:  
  
```CPP  
// Contains member data, open/is_open/close and buffer management functions.  
template<typename Device, typename Codecvt, typename Alloc>  
struct code_converter_impl {  
    void close(BOOST_IOS::openmode which)  
    {  
// BUG: Condition block is called only when which is either in or out but cannot be  
// combination of both  
        if (which == BOOST_IOS::in && (flags_ & f_input_closed) == 0) {  
...  
        }  
        if (which == BOOST_IOS::out && (flags_ & f_output_closed) == 0) {  
    ....  
            );  
        }  
    }  
```  
When the `which` argument has the value `which = in | out` which is the default value provided by `code_convert::close` no condition block is called at all.  
The result is the device is not closed and data in the buffer are lost.
