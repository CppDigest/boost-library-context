# #109 - xml_iarchive in broken state after archive_exception is thrown [Open]

> Username: tsondergaard  
> Created at: 2018-06-15 09:53:38 UTC  
> Updated at: 2018-11-04 10:55:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/109  

The following example works with   
  
```  
#include <iostream>  
#include <sstream>  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
  
int main()  
{  
    std::ostringstream os;  
    {  
        boost::archive::xml_oarchive archive(os);  
  
        int a = 42;  
        archive & boost::serialization::make_nvp("foo", a);  
  
        std::cout << " Wrote a=" << a << " to archive\n";  
    }  
  
    std::cout << "archive xml: " << os.str() << "\n";  
    try {  
        std::istringstream is(os.str());  
        boost::archive::xml_iarchive archive(is);  
        int a = 0;  
        archive & boost::serialization::make_nvp("foo", a);  
        try {  
            archive & boost::serialization::make_nvp("not_there", a);  
        } catch (const boost::archive::archive_exception &) {  
            std::cout << "exception 1\n";  
        }  
  
        std::cout << " Read a=" << a << " from archive\n" << std::endl;  
  
    } catch (...) {  
        std::cout << "exception 2\n";  
    }  
}  
```  
  
In a centos:7 docker container with the distro boost-serialization-1.53.0 this outputs what I would expect:  
  
```  
[root@ea27e53f5ba4 ~]# g++ -std=c++14 -Wall -g -lboost_serialization -fPIC -o boost-serialization-example boost-serialization-example.cc && ./boost-serialization-example  
g++: error: unrecognized command line option '-std=c++14'  
[root@ea27e53f5ba4 ~]# g++ -std=c++11 -Wall -g -lboost_serialization -fPIC -o boost-serialization-example boost-serialization-example.cc && ./boost-serialization-example  
 Wrote a=42 to archive  
archive xml: <?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="10">  
<foo>42</foo>  
</boost_serialization>  
  
  
exception 1  
 Read a=42 from archive  
```  
  
On fedora 28 with the distro boost-serialization-1.66.0 the output is this:  
  
```  
 Wrote a=42 to archive  
archive xml: <?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="16">  
<foo>42</foo>  
</boost_serialization>  
  
  
exception 1  
 Read a=42 from archive  
  
terminate called after throwing an instance of 'boost::archive::archive_exception'  
  what():  input stream error-Success  
Aborted (core dumped)  
```  
  
Note that the second exception is not caught by the outer catch (...) indicating that the process is in a corrupted state. In other words it is not just that the xml_iarchive::~xml_iarchive() also throws an exception, because that exception *should* be caught by the outer block.  
  
Also note that the xml does have the closing </boost_serialization> tag so this is not a duplicate of #82.  
  
I guess the special thing here is is that the exception is caught, but the xml_archive instance stays in scope. If the xml_archive is destroyed while the exception is being thrown the destructor does nothing and the problem goes away:  
  
```  
template<class Archive>  
BOOST_ARCHIVE_DECL  
xml_iarchive_impl<Archive>::~xml_iarchive_impl(){  
    if(std::uncaught_exception())  
        return;  
    if(0 == (this->get_flags() & no_header)){  
        gimpl->windup(is);  
    }  
}  
```

---

## Comment 1

> Username: SteffenL  
> Created at: 2018-11-04 10:55:13 UTC  
> Updated at: 2018-11-04 10:55:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/109#issuecomment-435659678  

I am seeing something similar when using Boost 1.66 through 1.68. I do not recall which version of Boost I used before but this was not a problem then.  
  
I have some code like this:  
  
    try {  
        using boost::serialization::make_nvp;  
        boost::archive::xml_iarchive ar(is);  
        ar & make_nvp("a", a);  
        try {  
            ar & make_nvp("b", b);  
        } catch (boost::archive::archive_exception& ex) {  
            // This tag may not exist but I'm not sure how to properly handle it  
            // Suppress the exception  
            if (ex.code != boost::archive::archive_exception::input_stream_error) {  
                throw;  
            }  
        }  
    } catch (boost::archive::archive_exception&) {  
        throw;  
    }  
  
When `ar` is being destroyed, `archive_exception` is thrown in `~xml_iarchive()`, and it cannot be caught.
