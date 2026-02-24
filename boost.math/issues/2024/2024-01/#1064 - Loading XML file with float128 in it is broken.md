# #1064 - Loading XML file with float128 in it is broken. [Closed]

> Username: cosurgi  
> Created at: 2024-01-13 19:00:19 UTC  
> Updated at: 2024-01-21 10:21:48 UTC  
> Closed at: 2024-01-21 10:21:48 UTC  
> Url: https://github.com/boostorg/math/issues/1064  

Hi,  
  
Using boost 1.74 here. I cannot load an XML file with float128 in it. Saving works. Try this example:  
  
```  
#include <boost/cstdfloat.hpp> // must be the first include  
#include <boost/multiprecision/float128.hpp>  
#include <complex>  
using Real    = boost::float128_t;  
using Complex = std::complex<Real>;  
  
#include <boost/algorithm/string.hpp>  
#include <boost/archive/codecvt_null.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/iostreams/device/file.hpp>  
#include <boost/iostreams/filtering_stream.hpp>  
#include <boost/serialization/complex.hpp>  
#include <locale>  
#include <string>  
  
#if BOOST_VERSION >= 104700 // To correctly recognize serialization of Inf and NaN numbers, there are different includes for different boost versions  
#include <boost/math/special_functions/nonfinite_num_facets.hpp>  
#else  
#include <boost/math/nonfinite_num_facets.hpp>  
#endif  
namespace IO {  
template <class T, class oarchive> void save(std::ostream& ofs, const std::string& objectTag, T& object)  
{  
	std::locale default_locale(std::locale::classic(), new boost::archive::codecvt_null<char>);  
	std::locale locale2(default_locale, new boost::math::nonfinite_num_put<char>);  
	ofs.imbue(locale2);  
	oarchive oa(ofs, boost::archive::no_codecvt);  
	oa << boost::serialization::make_nvp(objectTag.c_str(), object);  
	ofs.flush();  
}  
template <class T, class iarchive> void load(std::istream& ifs, const std::string& objectTag, T& object)  
{  
	std::locale default_locale(std::locale::classic(), new boost::archive::codecvt_null<char>);  
	std::locale locale2(default_locale, new boost::math::nonfinite_num_get<char>);  
	ifs.imbue(locale2);  
	iarchive ia(ifs, boost::archive::no_codecvt);  
	ia >> boost::serialization::make_nvp(objectTag.c_str(), object);  
}  
template <class T> void save(const std::string fileName, const std::string& objectTag, T& object)  
{  
	boost::iostreams::filtering_ostream out;  
	out.push(boost::iostreams::file_sink(fileName));  
	if (!out.good()) throw std::runtime_error("Error opening file " + fileName + " for writing.");  
	save<T, boost::archive::xml_oarchive>(out, objectTag, object);  
}  
template <class T> void load(const std::string& fileName, const std::string& objectTag, T& object)  
{  
	boost::iostreams::filtering_istream in;  
	in.push(boost::iostreams::file_source(fileName));  
	if (!in.good()) throw std::runtime_error("Error opening file " + fileName + " for reading.");  
	load<T, boost::archive::xml_iarchive>(in, objectTag, object);  
}  
};  
  
int main()  
{  
	Complex num(1, 2);  
	IO::save("saveNum.xml", "num", num);  
	Complex read;  
	IO::load("saveNum.xml", "num", read);  
	if (num != read) exit(1);  
}  
```  
  
I narrowed down the problem to [this line](https://github.com/boostorg/math/blob/develop/include/boost/math/cstdfloat/cstdfloat_iostream.hpp#L146). When I added right before it to print `str` it printed:  
  
```  
1.000000000000000000000000000000000000e+00</real>  
```  
  
note the `</real>` at the end. Hence [this line](https://github.com/boostorg/math/blob/develop/include/boost/math/cstdfloat/cstdfloat_iostream.hpp#L146) will trigger an error, because parsing the string did not reach the end of the string.  
  
I am compiling it with: `g++ -O1 -g testLoadFloat128.cpp -o testLoadFloat128 -std=gnu++17 -Wall -Werror=narrowing -lquadmath -lboost_serialization -lboost_system -lboost_filesystem -lboost_iostreams`

---

## Comment 1

> Username: cosurgi  
> Created at: 2024-01-13 19:04:31 UTC  
> Url: https://github.com/boostorg/math/issues/1064#issuecomment-1890721876  

In example above I used `Complex` number. In fact the problem can be reduced to even simpler case with real numbers. Use this `int main()` instead  
  
```  
int main()  
{  
	Real num = 1;  
	IO::save("saveNum.xml", "num", num);  
	Real read;  
	IO::load("saveNum.xml", "num", read);  
	if (num != read) exit(1);  
}  
```

---

## Comment 2

> Username: NAThompson  
> Created at: 2024-01-20 18:17:02 UTC  
> Url: https://github.com/boostorg/math/issues/1064#issuecomment-1902219413  

@cosurgi : Migrate to multiprecision?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-01-20 18:26:04 UTC  
> Url: https://github.com/boostorg/math/issues/1064#issuecomment-1902224746  

>Migrate to multiprecision?  
  
No actually I think this is Math and cstdfloat.hpp, wil investigate...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-01-20 18:39:53 UTC  
> Url: https://github.com/boostorg/math/issues/1064#issuecomment-1902236292  

Minimal test case:  
  
```  
#include <sstream>  
#include <iostream>  
#include <boost/cstdfloat.hpp>  
  
  
int main()   
{  
   using real = boost::float128_t;  
  
   try {  
      std::stringstream ss;  
      real v = 2;  
      ss << v << "<real>";  
      real v2;  
      ss >> v2;  
      std::cout << v2 << std::endl;  
   }  
   catch (const std::exception& e)  
   {  
      std::cout << e.what() << std::endl;  
   }  
}  
```  
  
Outputs:  
  
```  
Unable to interpret input string as a boost::float128_t  
```  
  
where as with float64_t outputs "2" as expected.  
  
Also works correctly with `boost::multiprecision::float128`.
