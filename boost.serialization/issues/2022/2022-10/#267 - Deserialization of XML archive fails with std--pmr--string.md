# #267 - Deserialization of XML archive fails with std::pmr::string [Open]

> Username: schorsch1976  
> Created at: 2022-10-11 06:38:48 UTC  
> Updated at: 2022-11-15 12:56:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/267  

Expected Behaviour: Serialization and deserialization of std::pmr::string works like std::string  
  
Observed Behaviour: Exception is thrown at the deserialization of a std::pmr::string. The string is expected to be: "D1" but it is `"D1</value>"`.  
  
Reproduction Steps:  
  
```  
// STL Archive + Stuff  
#include <boost/serialization/export.hpp>  
  
// PMR Strings  
#include <boost/config.hpp>  
#include <boost/serialization/level.hpp>  
#include <string>  
#include <memory_resource>  
BOOST_CLASS_IMPLEMENTATION(std::pmr::string, boost::serialization::primitive_type)  
BOOST_CLASS_IMPLEMENTATION(std::pmr::wstring, boost::serialization::primitive_type)  
  
// include headers that implement a archive in xml format  
#include <boost/archive/archive_exception.hpp>  
  
  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
  
using OArchive = boost::archive::xml_oarchive;  
using IArchive = boost::archive::xml_iarchive;  
  
#include <sstream>  
using ArchiveIStringStream = std::istringstream;  
using ArchiveOStringStream = std::ostringstream;  
  
// -----------------------------------  
#include <compare>  
#include <cassert>  
  
#if 1  
// Trigger the Bug with that typedef  
using String = std::pmr::string;  
#else  
// This Typedef works  
using String = std::string;  
#endif  
struct Data  
{  
    friend class boost::serialization::access;  
  
    // When the class Archive corresponds to an output archive, the  
    // & operator is defined similar to <<.  Likewise, when the class Archive  
    // is a type of input archive the & operator is defined similar to >>.  
    template<class Archive>  
    void serialize(Archive& ar, const unsigned int version)  
    {  
        ar& BOOST_SERIALIZATION_NVP(value);  
        ar& BOOST_SERIALIZATION_NVP(test);  
    }  
  
    auto operator<=>(const Data& rhs) const = default;  
    String value = "default";  
    int test = 1;  
};  
BOOST_CLASS_VERSION(Data, 0)  
  
std::string ToString(const Data& data)  
{  
    ArchiveOStringStream oss(std::ios::binary);  
  
    {  
        OArchive oa(oss);  
        oa << BOOST_SERIALIZATION_NVP(data);  
    }  
  
    return oss.str();  
}  
  
Data FromString(std::string& s)  
{  
    Data data;  
    ArchiveIStringStream iss(s, std::ios::binary);  
    {  
        IArchive ia(iss);  
        ia >> BOOST_SERIALIZATION_NVP(data);  
    }  
    return data;  
}  
  
#include <iostream>  
  
int main()  
{  
    try  
    {  
        Data d1;  
        d1.value = "D1";  
        d1.test = 1;  
  
        auto s1 = ToString(d1);  
        auto r1 = FromString(s1);  
  
        assert(d1 == r1);  
  
        std::cout << "Success: Is Equal: " << (d1 == r1) << "\n";  
        return d1 == r1 ? EXIT_SUCCESS : EXIT_FAILURE;  
    }  
    catch (const std::exception& ex)  
    {  
        std::cerr << "Should not happen: " << ex.what() << "\n";  
    }  
    catch (...)  
    {  
        std::cerr << "Should not happen: unknown exception\n";  
    }  
    return EXIT_FAILURE;  
}  
  
```  
  
  
  
OS: Windows 10 21H2 x64  
Compiler: MSVC 2019 (16.11.9)  
Boost: 1.80.0

---

## Comment 1

> Username: schorsch1976  
> Created at: 2022-10-13 15:45:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/267#issuecomment-1277823261  

I wrote a test on the current develop branch that also showed failure in the serializtion to/from the binary_archive.  
  
```  
// PMR Strings  
// future <boost/serialization/pmr_string.hpp>  
#include <boost/config.hpp>  
#include <boost/serialization/level.hpp>  
#include <string>  
#include <memory_resource>  
BOOST_CLASS_IMPLEMENTATION(std::pmr::string, boost::serialization::primitive_type)  
BOOST_CLASS_IMPLEMENTATION(std::pmr::wstring, boost::serialization::primitive_type)  
  
#include <boost/archive/archive_exception.hpp>  
  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/archive/binary_iarchive.hpp>  
  
#include <sstream>  
  
// -----------------------------------  
#include <cassert>  
  
#include "test_tools.hpp"  
  
template <typename String>  
struct Data  
{  
    friend class boost::serialization::access;  
  
    template<class Archive>  
    void serialize(Archive& ar, const unsigned int /* version */)  
    {  
        ar& BOOST_SERIALIZATION_NVP(value);  
        ar& BOOST_SERIALIZATION_NVP(test);  
    }  
  
    bool operator==(const Data& rhs) const  
	{  
		return value == rhs.value && test == rhs.test;  
	}  
  
    String value = "default";  
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
        d1.value = "D1";  
        d1.test = 1;  
  
        auto s1 = ToString<OArchive, String>(d1);  
        auto r1 = FromString<IArchive, String>(s1);  
  
        BOOST_CHECK(d1 == r1);  
  
		return 0;  
    }  
    catch (...)  
    {  
		// this is always an error  
        BOOST_CHECK("boost::archive_exception caught" && false);  
    }  
  
	return 1;  
}  
  
int test_main( int /* argc */, char* /* argv */[] )  
{  
	int result = 0;  
  
	result += PerformTest<std::string, 		boost::archive::xml_oarchive, boost::archive::xml_iarchive>();  
	result += PerformTest<std::string, 		boost::archive::text_oarchive, boost::archive::text_iarchive>();  
	result += PerformTest<std::string, 		boost::archive::binary_oarchive, boost::archive::binary_iarchive>();  
  
	result += PerformTest<std::pmr::string, boost::archive::text_oarchive, boost::archive::text_iarchive>();  
  
	// These two tests fail on two different ways  
	result += PerformTest<std::pmr::string, boost::archive::xml_oarchive, boost::archive::xml_iarchive>();  
	result += PerformTest<std::pmr::string, boost::archive::binary_oarchive, boost::archive::binary_iarchive>();  
  
    return result == 0 ? EXIT_SUCCESS : EXIT_FAILURE;  
}  
  
```

---

## Comment 2

> Username: schorsch1976  
> Created at: 2022-11-15 12:56:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/267#issuecomment-1315272944  

I could resolv my issue without modifing boost::serialization as i introduced a shallow type to wrap my string data.
