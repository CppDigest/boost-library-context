# #298 - serialization/deserialization of std::wstring with xml archive fails with unicode characters [Open]

> Username: schorsch1976  
> Created at: 2023-08-30 09:46:44 UTC  
> Updated at: 2023-08-30 09:46:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/298  

During my test for #267 i found an issue in the current implementation. Here is the code to reproduce it. The chinease utf-8 chars trigger that behaviour.   
  
My build environment:  
modular-boost: 1.83.0   
serialization commit id: 0ca603daf99888bf059c01ae1bab1b27dbc35ebe  
MSVC 2019 + /std:c++17  
Windows 10 x64 22H2  
Buildtype: x64  
  
```  
#include <boost/config.hpp>  
#include <boost/serialization/string.hpp>  
  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/archive/binary_iarchive.hpp>  
  
#include <sstream>  
#include <algorithm>  
#include <cassert>  
  
// -----------------------------------  
#include <iostream>  
#include <thread>  
  
// Test with some unicode characters  
template <typename T>  
struct TestTraits  
{  
};  
template <>  
struct TestTraits<char>  
{  
	static constexpr char DefaultValue[] = "Default";  
	static constexpr char TestValue[] =  
		u8"Hello boost! The author of boost::serialization is Robert Ramey.\n"  
		u8"Hallo boost! Der Autor von boost::serialization ist Robert Ramey.\n"  
		u8"你好 boost！ boost::serialization 由 Robert Ramey 编写。";  
};  
  
#ifndef BOOST_NO_STD_WSTRING  
template <>  
struct TestTraits<wchar_t>  
{  
	static constexpr wchar_t DefaultValue[]  = L"Default";  
	static constexpr wchar_t TestValue[]  =  
		L"Hello boost! The author of boost::serialization is Robert Ramey.\n"  
		L"Hallo boost! Der Autor von boost::serialization ist Robert Ramey.\n"  
		L"你好 boost！ boost::serialization 由 Robert Ramey 编写。";  
};  
#endif  
  
template <typename String>  
struct Data  
{  
	template<class Archive>  
	void serialize(Archive& ar, const unsigned int /* version */)  
	{  
		ar& BOOST_SERIALIZATION_NVP(value);  
		ar& BOOST_SERIALIZATION_NVP(test);  
	}  
  
	bool operator==(const Data& rhs) const  
	{  
		// compare by value as the capacity is irrelevant  
		return std::equal(std::begin(value), std::end(value), std::begin(rhs.value)) == true &&  
			test == rhs.test;  
	}  
  
	String value = TestTraits<typename String::value_type>::DefaultValue;  
	int test = 1;  
};  
  
template <typename Archive, typename T>  
std::string ToString(const Data<T>& data)  
{  
	std::ostringstream oss(std::ios::binary);  
  
	{  
		Archive oa(oss);  
		oa << BOOST_SERIALIZATION_NVP(data);  
	}  
  
	return oss.str();  
}  
  
template <typename Archive, typename T>  
Data<T> FromString(std::string& s)  
{  
	Data<T> data;  
	std::istringstream iss(s, std::ios::binary);  
	{  
		Archive ia(iss);  
		ia >> BOOST_SERIALIZATION_NVP(data);  
	}  
	return data;  
}  
  
template <typename String, typename OArchive, typename IArchive>  
int PerformTest()  
{  
	try  
	{  
		Data<String> d1;  
		d1.value = TestTraits<typename String::value_type>::TestValue;  
		d1.test = 1;  
  
		auto s1 = ToString<OArchive, String>(d1);  
		auto r1 = FromString<IArchive, String>(s1);  
  
		auto p_d1 = d1.value.c_str();  
		auto p_r1 = r1.value.c_str();  
  
		auto l_d1 = d1.value.size();  
		auto l_r1 = r1.value.size();  
  
		// Bug:  
		// String = std::wstring  
		// OArchive =boost::archive::xml_oarchive  
		// IArchive = boost::archive::xml_iarchive  
		assert(d1 == r1);  
  
		if (d1 == r1)  
		{  
			return 0;  
		}  
	}  
	catch (const std::exception& ex)  
	{  
		std::cerr << ex.what() << '\n';  
	}  
  
	return 1;  
}  
  
int main( int /* argc */, char* /* argv */[] )  
{  
	int result = 0;  
  
	// working  
	result += PerformTest<std::string, 		boost::archive::xml_oarchive,		boost::archive::xml_iarchive>();  
	result += PerformTest<std::string, 		boost::archive::text_oarchive,		boost::archive::text_iarchive>();  
	result += PerformTest<std::string, 		boost::archive::binary_oarchive,	boost::archive::binary_iarchive>();  
  
#ifndef BOOST_NO_STD_WSTRING  
	result += PerformTest<std::wstring, 	boost::archive::text_oarchive, 		boost::archive::text_iarchive>();  
	result += PerformTest<std::wstring, 	boost::archive::binary_oarchive,	boost::archive::binary_iarchive>();  
  
	// Bug  
	result += PerformTest<std::wstring, 	boost::archive::xml_oarchive, 		boost::archive::xml_iarchive>();  
#endif // BOOST_NO_STD_WSTRING  
  
	if (result != 0)  
	{  
		std::cerr << "Bug triggered: Expected Result 0 but it is " << result << '\n';  
	}  
	return result == 0 ? EXIT_SUCCESS : EXIT_FAILURE;  
}  
  
```
