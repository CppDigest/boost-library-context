# #84 - logging pointer to struct [Closed]

> Username: fantastory  
> Created at: 2019-05-14 17:37:24 UTC  
> Updated at: 2019-05-25 18:42:55 UTC  
> Closed at: 2019-05-25 18:42:55 UTC  
> Url: https://github.com/boostorg/log/issues/84  

What I want to do is:  
```  
struct A {  
  int a;  
};  
  
A *theA = getPointerToA();  
  
//ostream operator defined externally since I do not own struct A code  
std::ostream &operator<<(std::ostream& stream, const A*  item) {  
  if (item) stream<<item->a;  
  else stream<<"null A";   
  return stream;   
}  
  
BOOST_LOG_TRIVIAL(info) << theA;  
```  
  
But as we look into  
lib\native\include\boost\log\utility\formatting_ostream.hpp  
there is an       
basic_formatting_ostream& operator<< (const void* value);  
defined which takes precedence over the one defined by me (visual studio 2017)  
  
I can solve it by logging reference instead of pointer, but being it a diagnostic function I would like to check for nullptr.  
  
```  
std::ostream &operator<<(std::ostream& stream, const A&  item) {  
  if (&item) stream<<item->a;  
  else stream<<"null A";   
  return stream;   
}  
BOOST_LOG_TRIVIAL(info) << *theA;  
```  
This actually works on visual studio, but it breaks non null reference, which is not allowed on clang.  
  
Finaly I can declare operator<< as a template  
  
```  
template<class OST> std::enable_if_t<std::is_same_v<char, typename OST::char_type>, OST&> operator<<(OST& stream, const A*      item)   
{  
  if (item) stream<<item->a;  
  else stream<<"null A";   
  return stream;   
}  
```  
  
It works well, but stream operators are used everywhere in a projects and it greatly lengthes compilation.  
  
Would it be possible to remove   
basic_formatting_ostream& operator<< (const void* value);  
from  
lib\native\include\boost\log\utility\formatting_ostream.hpp  
?

---

## Comment 1

> Username: Lastique  
> Created at: 2019-05-14 17:55:32 UTC  
> Updated at: 2019-05-14 17:56:15 UTC  
> Url: https://github.com/boostorg/log/issues/84#issuecomment-492342574  

> Would it be possible to remove `basic_formatting_ostream& operator<< (const void* value);`?  
  
That overload reflects the similar overload in `std::basic_ostream`, see [ostream.inserters.arithmetic]. Given that `basic_formatting_ostream` is supposed to mirror `std::basic_ostream`, I can't just remove the overload. But I think I could try to forward the original pointer type to the `std::basic_ostream` inserter so that your overload gets picked up instead of the one for `const void*`.  
  
In the meantime, you can work around the problem by adding an overload like this:  
  
```  
template< typename Char, typename Traits, typename Allocator >  
basic_formatting_ostream< Char, Traits, Allocator >& operator<< (  
    basic_formatting_ostream< Char, Traits, Allocator >& stream, const A* value)  
{  
    stream.stream() << value;  
    return stream;  
}  
```  
  
It is more preferable than the `const void*` overload and should be picked by the compiler. It will reuse your existing overload for `std::ostream`.

---

## Comment 2

> Username: fantastory  
> Created at: 2019-05-15 09:39:57 UTC  
> Url: https://github.com/boostorg/log/issues/84#issuecomment-492583041  

Declaring:  
```  
#include "pch.h"  
#include <boost/test/unit_test.hpp>  
#include <boost/log/trivial.hpp>  
  
struct LoggerTestStructA {  
    int a;  
};  
std::ostream& operator<<(std::ostream& stream, const LoggerTestStructA *item) {  
    stream << __FUNCTION__;  
    return stream;  
}  
  
template< typename Traits, typename Allocator >  
boost::log::basic_formatting_ostream< char, Traits, Allocator >& operator<< (  
        boost::log::basic_formatting_ostream< char, Traits, Allocator >& stream, const LoggerTestStructA* value)  
{  
    stream.stream() << __FUNCTION__;  
    return stream;  
}  
  
BOOST_AUTO_TEST_CASE(LoggerTest) {  
    LoggerTestStructA *itemA = nullptr;  
    void *voidPointer = nullptr;  
    BOOST_LOG_TRIVIAL(info) << "initializing";  
    BOOST_LOG_TRIVIAL(info) << "itemA:"<< itemA;  
    BOOST_LOG_TRIVIAL(info) << "voidPointer:" << voidPointer;  
}  
```  
Does not compile with:  
error C2666: 'boost::log::v2s_mt_nt6::basic_record_ostream<char>::operator <<': 3 overloads have similar conversions ...

---

## Comment 3

> Username: fantastory  
> Created at: 2019-05-15 09:42:04 UTC  
> Url: https://github.com/boostorg/log/issues/84#issuecomment-492583771  

I wondered if   
basic_formatting_ostream& operator<< (const void* value);  
is really needed, since without it compiler could use operator<<(void *) from std.  
But as I removed it the code uses basic_formatting_ostream& operator<< (const bool value);  
Instead of my operator.

---

## Comment 4

> Username: Lastique  
> Created at: 2019-05-15 12:04:02 UTC  
> Url: https://github.com/boostorg/log/issues/84#issuecomment-492625977  

> Does not compile with:  
> error C2666: 'boost::log::v2s_mt_nt6::basic_record_ostream::operator <<': 3 overloads have similar conversions ...  
  
Right, another overload is needed for `basic_record_ostream`:  
  
```  
boost::log::record_ostream& operator<< (  
        boost::log::record_ostream& stream, const LoggerTestStructA* value)  
{  
    stream.stream() << __FUNCTION__;  
    return stream;  
}  
```  
  
Actually, depending on what formatters you set, you may not need the overload for `basic_formatting_ostream` and the one for `record_ostream` may be enough.  
  
> I wondered if  
> basic_formatting_ostream& operator<< (const void* value);  
> is really needed, since without it compiler could use operator<<(void *) from std.  
  
`basic_formatting_ostream` does not derive from `std::basic_ostream`, it has to forward the call to the `operator<<` for `std::basic_ostream`.

---

## Comment 5

> Username: fantastory  
> Created at: 2019-05-15 12:07:09 UTC  
> Url: https://github.com/boostorg/log/issues/84#issuecomment-492626946  

:) yeah i just figured it out  
basic_record_ostream is enaugh for me. I am clearing my example.

---

## Comment 6

> Username: fantastory  
> Created at: 2019-05-15 12:57:07 UTC  
> Url: https://github.com/boostorg/log/issues/84#issuecomment-492643006  

Code below is working for me. Great thanks :)  
And it allows to remove all templates - actually I know I am not using wchar_t.  
  
```  
#include "pch.h"  
#include <boost/log/trivial.hpp>  
#include <boost/test/unit_test.hpp>  
#include <sstream>  
  
struct LoggerTestA {  
    int a;  
};  
  
std::ostream& operator<<(std::ostream& stream, const LoggerTestA *item) {  
    stream << __func__;  
    return stream;  
}  
  
typedef boost::log::basic_record_ostream<char> BoostLogRecordStream;  
BoostLogRecordStream& operator<< (  
    BoostLogRecordStream& stream, const LoggerTestA* value)  
{  
    stream.stream() << __func__ << ":passingto: "<<value;  
    return stream;  
}  
  
BOOST_AUTO_TEST_CASE(LoggerTest) {  
    LoggerTestA *item = nullptr;  
    BOOST_LOG_TRIVIAL(info) << "initializing";  
    BOOST_LOG_TRIVIAL(info) << "item:"<<item;  
    BOOST_LOG_TRIVIAL(info) << item;  
  
    std::stringstream sstream;  
    sstream << item;  
    BOOST_LOG_TRIVIAL(info) << "stream:"<<sstream.str();  
}  
```
