# #100 - Process finished with exit code 139 (interrupted by signal 11: SIGSEGV) [Closed]

> Username: chudonghao  
> Created at: 2018-03-19 01:50:38 UTC  
> Updated at: 2018-03-19 02:21:48 UTC  
> Closed at: 2018-03-19 02:21:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/100  

###C++ CODE(study_boost_serialization.cpp)###  
```  
#include <iostream>  
#include <fstream>  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <boost/format.hpp>  
  
class gps_position {  
    friend class boost::serialization::access;  
  
    int degrees;  
    int minutes;  
    float seconds;  
    std::vector<int> ints;  
  
    template<class Archive>  
    void serialize(Archive &ar, const unsigned int /* file_version */) {  
        ar & BOOST_SERIALIZATION_NVP(degrees);  
        ar & BOOST_SERIALIZATION_NVP(minutes);  
        ar & BOOST_SERIALIZATION_NVP(seconds);  
        ar & BOOST_SERIALIZATION_NVP(ints);  
    }  
  
public:  
    gps_position() : degrees(1), minutes(2), seconds(3.f), ints() {  
        ints.push_back(4);  
        ints.push_back(5);  
    };  
  
    void reset() {  
        degrees = 0;  
        minutes = 0;  
        seconds = 0.f;  
        ints.resize(0);  
    }  
  
    void say() {  
        std::cout << boost::format("gps_position{degrees=%1%,minutes=%2%,seconds=%3%,ints.size=%4%}\n") % degrees %  
                     minutes % seconds % ints.size();  
    }  
};  
  
class location : public gps_position {  
    friend class boost::serialization::access;  
  
    std::string name;  
  
    template<class Archive>  
    void serialize(Archive &ar, const unsigned int /* file_version */) {  
        ar & BOOST_SERIALIZATION_BASE_OBJECT_NVP(gps_position);  
        ar & BOOST_SERIALIZATION_NVP(name);  
    }  
  
public:  
  
    location() : name("location") {}  
  
    void reset() {  
        gps_position::reset();  
        name.resize(0);  
    }  
  
    void say() {  
        gps_position::say();  
        std::cout << boost::format("location{name=%1%}\n") % name;  
    }  
};  
  
class FZ {  
    friend boost::serialization::access;  
  
    gps_position *l;  
  
    template<class Archive>  
    void serialize(Archive &ar, const unsigned int /* file_version */) {  
        ar & BOOST_SERIALIZATION_NVP(l);  
    }  
  
public:  
  
    FZ() {  
        l = new location;  
    }  
  
    void ref(FZ &right) {  
        delete l;  
        l = right.l;  
    }  
  
    void reset() {  
        delete l;  
        l = nullptr;  
    }  
  
    void say() {  
        std::cout << boost::format("FZ{l=@%1%}\n") % l;  
        if (l)  
            l->say();  
    }  
};  
  
using namespace std;  
  
int main() {  
    // create class instance  
    FZ fz;  
    FZ fz2;  
    fz2.ref(fz);  
#if SAVE  
    ofstream ofs("./test.xml");  
    // save data to archive  
    {  
        boost::archive::xml_oarchive oa(ofs);  
        // write class instance to archive  
        oa << BOOST_SERIALIZATION_NVP(fz);  
        oa << BOOST_SERIALIZATION_NVP(fz2);  
        // archive and stream closed when destructors are called  
    }  
    //  
    //// save data to archive  
    //{  
    //    boost::archive::text_oarchive oa(std::cout);  
    //    // write class instance to archive  
    //    oa << BOOST_SERIALIZATION_NVP(l);  
    //    // archive and stream closed when destructors are called  
    //}  
#endif  
#if LOAD  
    fz.reset();  
    fz2.reset();  
  
    ifstream ifs("./test.xml");  
    {  
        boost::archive::xml_iarchive ia(ifs);  
        ia >> BOOST_SERIALIZATION_NVP(fz);  
        ia >> BOOST_SERIALIZATION_NVP(fz2);  
    }  
    fz.say();  
    fz2.say();  
#endif  
    return 0;  
}  
```  
################  
###CMAKE CODE###  
```  
add_executable(study_boost_serialization_load study_boost_serialization.cpp)  
target_link_libraries(study_boost_serialization_load boost_serialization)  
target_compile_definitions(study_boost_serialization_load PRIVATE -DLOAD)  
  
add_executable(study_boost_serialization_save study_boost_serialization.cpp)  
target_link_libraries(study_boost_serialization_save boost_serialization)  
target_compile_definitions(study_boost_serialization_save PRIVATE -DSAVE)  
```  
###########################################  
###CONSOLE OUTPUT(study_boost_serialization_load)###  
Process finished with exit code 139 (interrupted by signal 11: SIGSEGV)  
###############  
###DEBUG func stack###  
##study_boost_serialization.cpp##  
```  
    ifstream ifs("./test.xml");  
    {  
        boost::archive::xml_iarchive ia(ifs);//error line  
        ia >> BOOST_SERIALIZATION_NVP(fz);  
        ia >> BOOST_SERIALIZATION_NVP(fz2);  
    }  
```  
##xml_iarchive.hpp##  
```  
class BOOST_SYMBOL_VISIBLE xml_iarchive :   
    public xml_iarchive_impl<xml_iarchive>{  
public:  
    xml_iarchive(std::istream & is, unsigned int flags = 0) :  
        xml_iarchive_impl<xml_iarchive>(is, flags)//error line  
    {}  
    ~xml_iarchive(){};  
};  
```
