# #289 - map_indexing_suite doesn't work with std::unordered_map<std::string, std::vector<double>> for MSVC 14.2 [Open]

> Username: brian-pantano  
> Created at: 2020-01-30 22:34:25 UTC  
> Updated at: 2020-05-21 12:25:18 UTC  
> Url: https://github.com/boostorg/python/issues/289  

This works on MSVC 14.1 (Visual Studio 17):  
  
    #define BOOST_PYTHON_STATIC_LIB  
  
    #include <boost/python.hpp>  
    #include <boost/python/suite/indexing/map_indexing_suite.hpp>  
    #include <boost/python/suite/indexing/vector_indexing_suite.hpp>  
    #include <string>  
    #include <unordered_map>  
    #include <vector>  
  
    BOOST_PYTHON_MODULE(HelloBoostPython)  
    {  
        using namespace boost::python;  
  
        class_<std::vector<double>>("DoubleVector")  
            .def(vector_indexing_suite<std::vector<double>>())  
            ;  
  
        class_<std::unordered_map<std::string, std::string>>("StringVecMap")  
            .def(map_indexing_suite<std::unordered_map<std::string, std::vector<double>>>())  
            ;  
    }  
  
But fails when building against the v142 (VS 19) Platform Toolset:   
  
    Severity	Code	Description	Project	File	Line	Suppression State  
    Error	C2039	'key_comp': is not a member of 'std::unordered_map<std::string,std::vector<double,std::allocator<double>>,std::hash<std::string>,std::equal_to<std::string>,std::allocator<std::pair<const std::string,std::vector<double,std::allocator<double>>>>>'	HelloBoostPython	C:\Misc\boost_1_71_0\boost\python\suite\indexing\map_indexing_suite.hpp	155	  
  
  
Change `std::unordered_map` to `std::map` and it works.  
  
Change `std::unordered_map<std::string, std::vector<double>>` to `std::unordered_map<std::string, std::string>>` and it works.

---

## Comment 1

> Username: ayaankhan98  
> Created at: 2020-05-21 12:25:18 UTC  
> Url: https://github.com/boostorg/python/issues/289#issuecomment-632057292  

can i work on this issue?
