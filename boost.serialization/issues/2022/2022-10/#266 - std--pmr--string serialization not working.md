# #266 - std::pmr::string serialization not working [Closed]

> Username: schorsch1976  
> Created at: 2022-10-10 13:30:58 UTC  
> Updated at: 2022-10-10 19:11:59 UTC  
> Closed at: 2022-10-10 19:11:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/266  

This does not compile, but i think it should compile  
  
```  
  
  #include <iostream>  
  #include <string>  
  #include <memory_resource>  
    
  #include <boost/serialization/nvp.hpp>  
  #include <boost/serialization/string.hpp>  
  #include <boost/archive/xml_oarchive.hpp>  
    
  int main()  
  {  
      std::pmr::string s = "Hello World\n";  
    
      boost::archive::xml_oarchive ar(std::cout);  
      ar & BOOST_SERIALIZATION_NVP(s);  
    
      std::cout << s;  
  }  
  
```  
When i add (before the archive include) then it compiles but the archives dont need an additional tag as it is a primitive type.  
  
```  
  #include <boost/serialization/nvp.hpp>  
  namespace boost::serialization {  
      // Wide PMR String  
      template<class Archive>  
      void serialize(Archive& ar, std::pmr::wstring& g, const unsigned int version)  
      {  
          ar& BOOST_SERIALIZATION_NVP(g);  
      }  
    
      // Ansi PMR String  
      template<class Archive>  
      void serialize(Archive& ar, std::pmr::string& g, const unsigned int version)  
      {  
          ar& BOOST_SERIALIZATION_NVP(g);  
      }  
    
  } // end namespace boost::serialization  
  
```  
  
I also tried to add  
  
```  
  BOOST_CLASS_IMPLEMENTATION(std::pmr::string, boost::serialization::primitive_type)  
  BOOST_CLASS_IMPLEMENTATION(std::pmr::wwstring, boost::serialization::primitive_type)  
```  
  
  
But also doesnt work.... What can i do?

---

## Comment 1

> Username: schorsch1976  
> Created at: 2022-10-10 13:36:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/266#issuecomment-1273324123  

Here is it on godbolt:  
  
https://godbolt.org/z/EhfE4n173

---

## Comment 2

> Username: schorsch1976  
> Created at: 2022-10-10 13:43:58 UTC  
> Url: https://github.com/boostorg/serialization/issues/266#issuecomment-1273334011  

Ups ... my error.
