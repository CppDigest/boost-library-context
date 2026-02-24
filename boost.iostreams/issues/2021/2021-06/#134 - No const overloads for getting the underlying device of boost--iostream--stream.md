# #134 - No const overloads for getting the underlying device of boost::iostream::stream [Open]

> Username: LimpSquid  
> Created at: 2021-06-19 21:30:40 UTC  
> Updated at: 2021-06-19 21:35:59 UTC  
> Url: https://github.com/boostorg/iostreams/issues/134  

There seem to be no `const` overloads for getting the underlying `Device` of `boost::iostream::stream`. It might not be as trivial as by adding the following two lines, because sub-sequential calls do not seem to implement the `const` overloads either.  
```cpp  
Device const & operator*() const { return *this->member; }  
Device const * operator->() const { return &*this->member; }  
```  
I'm not sure if there is some reason why there are no `const` overloads for these particular methods. As far as I can see there shouldn't be any issue to add them. Currently I can work around this problem by marking the stream in the example as `mutable`.  
  
In any case here an example: https://coliru.stacked-crooked.com/a/fe70537e096a2b0b.   
Or use the code below:  
```cpp  
#include <iostream>  
#include <boost/iostreams/categories.hpp>  
#include <boost/iostreams/stream.hpp>  
  
struct stream_sink  
{  
    using char_type = char;  
    using category = boost::iostreams::sink_tag;  
      
    stream_sink(std::ostream & os) :  
        stream(os)  
    {}  
      
    bool fail() const { return stream.fail(); }  
      
    std::streamsize write(const char * s, std::streamsize n)   
    {  
        stream.write(s, n);  
        return n;  
    }      
      
    std::ostream & stream;  
};  
  
struct cout_writer  
{  
    using stream_type = boost::iostreams::stream<stream_sink>;  
      
    stream_type stream{std::cout};  
      
    operator bool() { return !stream->fail(); }  
      
    // Does not compile as there are no const overloads like below:  
    // - Device const & operator*() const { ... }  
    // - Device const * operator->() const { ... }  
    operator bool() const { return !stream->fail(); }  
      
    cout_writer & operator<<(std::string_view s)  
    {  
        stream << s;  
        return *this;  
    }  
};  
  
int main()  
{  
    cout_writer writer;  
      
    writer << "foo\n";  
}  
```
