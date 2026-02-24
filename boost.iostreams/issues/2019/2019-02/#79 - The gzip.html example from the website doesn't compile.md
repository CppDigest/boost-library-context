# #79 - The gzip.html example from the website doesn't compile [Closed]

> Username: yurivict  
> Created at: 2019-02-18 20:31:25 UTC  
> Updated at: 2019-02-19 19:58:29 UTC  
> Closed at: 2019-02-19 19:58:29 UTC  
> Url: https://github.com/boostorg/iostreams/issues/79  

Example from the bottom of this page: https://www.boost.org/doc/libs/1_69_0/libs/iostreams/doc/classes/gzip.html fails to compile:  
```  
$ c++ -o gunzip gunzip.cpp -I /usr/local/include/  
gunzip.cpp:12:5: error: no template named 'filtering_streambuf'; did you mean 'boost::iostreams::filtering_streambuf'?  
    filtering_streambuf<input> in;  
    ^~~~~~~~~~~~~~~~~~~  
    boost::iostreams::filtering_streambuf  
/usr/local/include/boost/iostreams/filtering_streambuf.hpp:60:41: note: 'boost::iostreams::filtering_streambuf' declared here  
BOOST_IOSTREAMS_DEFINE_FILTER_STREAMBUF(filtering_streambuf, boost::iostreams::chain, char)  
                                        ^  
```  
  
The example looks like it should produce an executable.  
  
boost-libs-1.69.0_1 on FreeBSD 11.2
