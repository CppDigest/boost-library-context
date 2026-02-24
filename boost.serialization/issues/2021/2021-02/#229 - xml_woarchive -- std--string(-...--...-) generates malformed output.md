# #229 - xml_woarchive << std::string("...\"...") generates malformed output [Open]

> Username: hajokirchhoff  
> Created at: 2021-02-26 15:22:20 UTC  
> Updated at: 2022-01-23 17:44:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/229  

Serializing a string to an xml_woarchive will produce an invalid archive if there are quotable character near a "multiple of 32" boundary.  
  
Example:  
xml_woarchive << nvp("err", string("01234567890123456789012345678\"-error-"));  
produces something like this:  
```  
... xml serialization header ...  
<err>01234567890123456789012345678&qu&quot;-error-</err>  
```  
Note the &qu&quot; string in the output.  
  
Here is a minimal sample:  
```  
  
#include <iostream>  
#include <sstream>  
#include "boost/archive/xml_woarchive.hpp"  
  
int main()  
{  
    std::string instring("01234567890123456789012345678\"-here-is-the-error");  
  
    std::wostringstream outstream;  
    {  
        boost::archive::xml_woarchive ar(outstream);  
        ar& boost::serialization::make_nvp("err", instring);  
    }  
    auto result = outstream.str();  
  
	/*  
	result will contain this:  
  
	<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="18">  
<err>01234567890123456789012345678&qu&quot;-here-is-the-error</err>  
</boost_serialization>  
  
  
    The bug is    <err>...678&qu&quot;...  
	A partial &quot; is written to the output, then a full &quot; is written again.  
	*/  
}  
```  
  
This is caused by wrapping the xml_escape<char const*> iterator within the wchar_from_mb<...> iterator.  
  
Here:  
boost::archive::save_iterator<char const *>(std::basic_ostream<wchar_t,std::char_traits<wchar_t>> & os, const char * begin, const char * end) Line 58  
	at x:\...\vcpkg\buildtrees\boost-serialization\src\ost-1.75.0-e02749c5e1.clean\include\boost\archive\impl\xml_woarchive_impl.ipp(58)  
```  
   std::copy(  
      xmbtows(begin),  
      xmbtows(end),  
      boost..ostream_iterator(os)  
   );  
```  
  
This `copy` statement copies the input from the string to the archive. The parameters to this statement are "by-value".  
  
Here is my first analysis as to the cause:  
  
The cause is the fact that the `wchar_from_mb` iterator has an internal buffer of 32 characters, which it fills from the xml_escape iterator. This works fine unless the xml_escape iterator returns a sequence where a quoted character needs more than the space of 32 characters for the quote to be complete. If that is the case, the wchar_from_mb iterator fills its internal buffer, but the last quote is truncated, as seen in the example: `091234...8&qu`. The buffer is full, so the escape sequence is incomplete.  
  
But `copy` uses the iterators 'by-value', so the iterator with the incomplete buffer is discarded. The next 32 characters will then start again with a full `&quot;`.

---

## Comment 1

> Username: hajokirchhoff  
> Created at: 2021-02-26 17:16:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/229#issuecomment-786778766  

The copy constructor of xml_escape is broken. See xml_escape.hpp:45  
```  
    // intel 7.1 doesn't like default copy constructor  
    xml_escape(const xml_escape & rhs) :  
        super_t(rhs.base_reference())  
    {}  
```  
This will not copy the `super_t` part of the xml_escape object. It will initialize the `super_t` base class with `rhs.base_reference()`, which is not the same as actually creating a copy.  
`super_t` is `escape<xml_excape<const char*>, const char*>`.  
  
`super_t(rhs.base_reference())` will call the constructor of `escape<...>(const char*).` in escape.hpp:101  
```  
    escape(Base base) :  
        super_t(base),  
        m_bnext(NULL),  
        m_bend(NULL),  
        m_full(false),  
        m_current_value(0)  
    {  
    }  
```  
Now it becomes clear why it does not work as expected:  
  
When the `wchar_from_mb` iterator gets created for the string, it will immediately produce the first 32 escaped chars. The base class `escape` is not finished escaping the " char. It still contains some more characters and `m_bnext` is !=NULL, `m_bend`!=NULL and especially `m_full==true`.  
  
This information is lost when the iterator gets copied. As a result, the copy thinks it has to escape the character again.  
  
So this for-loop inside std::copy will fail to produce the desired results: (xutility, Visual Studio 2017, around line 2440)  
```  
	_OutIt copy(_InIt _First, _InIt _Last, _OutIt _Dest)  
	{	// copy [_First, _Last) to [_Dest, ...)  
	_Adl_verify_range(_First, _Last);  
	const auto _UFirst = _Get_unwrapped(_First);  
	const auto _ULast = _Get_unwrapped(_Last);  
	const auto _UDest = _Get_unwrapped_n(_Dest, _Idl_distance<_InIt>(_UFirst, _ULast));  
```  
  
`_UFirst = _Get_unwrapped(_First)` copies the iterator. The faulty copy constructor drops information and result is as seen in the minimal sample.  
  
This happens with boost 1.75, Visual Studio 2017, x64 in both, Debug and Release builds.

---

## Comment 2

> Username: hajokirchhoff  
> Created at: 2021-03-01 10:39:32 UTC  
> Updated at: 2021-03-01 10:40:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/229#issuecomment-787844987  

Now confirmed: If I remove the `xml_escape` copy constructor (the one introduced as a workaround for Intel 7.1) and let the compiler generate a default copy constructor, the program runs fine.

---

## Comment 3

> Username: hajokirchhoff  
> Created at: 2021-03-01 10:42:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/229#issuecomment-787846759  

Since the Intel C++ compiler 7.1 was released around 2003 and the current Intel C++ Compiler version is around 19.0 I suggest you simply remove the faulty copy constructor and don't worry about this old compiler any more. It's highly unlikely anyone is still using Intel 7.1 and wanting to update to boost 1.76ff.

---

## Comment 4

> Username: hajokirchhoff  
> Created at: 2021-03-01 11:33:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/229#issuecomment-787879047  

(Pls. remove the .txt)  
  
[xml_escape-defective-copy-constructor.patch.txt](https://github.com/boostorg/serialization/files/6061200/xml_escape-defective-copy-constructor.patch.txt)

---

## Comment 5

> Username: hajokirchhoff  
> Created at: 2022-01-23 17:44:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/229#issuecomment-1019533030  

Is boost-serialization still maintained?  
<bump>
