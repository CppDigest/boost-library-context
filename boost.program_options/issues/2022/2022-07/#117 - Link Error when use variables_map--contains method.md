# #117 - Link Error when use variables_map::contains method [Open]

> Username: YunchengLiu  
> Created at: 2022-07-21 06:51:17 UTC  
> Updated at: 2025-01-17 04:53:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/117  

Env: `Windows 10 19044.1826`  
Library Config: `vcpkg`, all ports are updated to date.  
IDE: `Visual Studio 2022 17.2.6`  
Boost Version: `1.79.0`  
C++ Version: `C++20`  
  
When i use `program_options` library in my program, i got a link error as follows.  
```  
main.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: bool __cdecl std::_Tree<class std::_Tmap_traits<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,class boost::program_options::variable_value,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,class boost::program_options::variable_value > >,0> >::contains(class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)const " (__imp_?contains@?$_Tree@ V?$_Tmap_traits@V?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@Vvariable_value@program_options@boost@@U?$less@V?$basic_string@ DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@2@V?$allocator@U?$pair@$$CBV?$basic_string@DU?$char_traits@D@ std@@V?$allocator@D@2@@std@@Vvariable_value@program_options@boost@@@std @@@2@$0A@@std@@@std@@QEBA_NAEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@2@@Z), function "__cdecl This is referenced in exec(class boost::program_options::options_description const &,class boost::program_options::variables_map const &)" (?exec@@YA@AEBVoptions_description@program_options@boost@@AEBVvariables_map@23@@Z) symbol  
```  
  
Other boost libraries are work well with my vcpkg and VS env.  
  
The reproduce code:  
```  
#include <cstdlib>  
#include <iostream>  
#include <boost/program_options.hpp>  
  
namespace po = boost::program_options;  
  
auto initDesc(po::options_description& desc) {  
  
    desc.add_options()  
        ("--help", "Produce help message")  
        ;  
  
}  
  
auto exec(const po::options_description& desc, const po::variables_map& vm) {  
    // contains method will cause a link error  
    // but count method will perform normal  
   // Code is compiled with an 'std=c++20' option  
    if (vm.contains("--help")) {  
        std::cout << desc << std::endl;  
        return EXIT_FAILURE;  
    }  
  
    return EXIT_SUCCESS;  
  
}  
  
int main(int argc, char* argv[]) {  
  
    try {  
  
        po::options_description opts{ "All Supported Options" };  
        po::variables_map vm;  
  
        initDesc(opts);  
  
        po::store(po::parse_command_line(argc, argv, opts), vm);  
        po::notify(vm);  
  
        return exec(opts, vm);  
  
    }  
    catch (const std::exception& e) {  
        std::cerr << e.what() << std::endl;  
    }  
  
    return EXIT_SUCCESS;  
  
}  
  
```  
  
I noticed that `variables_map` are inherited from `std::map`, is something wrong with here?

---

## Comment 1

> Username: aleksmurfitt  
> Created at: 2023-05-09 16:41:28 UTC  
> Updated at: 2023-05-09 16:44:57 UTC  
> Url: https://github.com/boostorg/program_options/issues/117#issuecomment-1540515490  

This is quite an old issue, but you're getting this error because your Boost libraries aren't targeting C++20.   
  
As you correctly noted `variables_map` inherits from `std::map`, so while your compiler is happy to use C++20 methods like `std::map::contains`, if Boost isn't being built for C++20 then the symbols won't be available at link time. I would look into how `vcpkg` builds Boost - it's almost certainly targeting an older standard.  
  
In the meantime, `vm.find("--help") != vm.end()` will work just fine and is exactly the same as `vm.contains("--help")`, albeit less convenient.

---

## Comment 2

> Username: chitao1234  
> Created at: 2025-01-17 04:48:30 UTC  
> Updated at: 2025-01-17 04:53:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/117#issuecomment-2597428525  

Would dropping BOOST_PROGRAM_OPTIONS_DECL on types help? Currently, BOOST_PROGRAM_OPTIONS_DECL is used on classes like variables_map and variable_value, which would make the types part of the DLL interface, causing linking problems like this.  
I removed it from the class definition and friend class declaration, and it links. It would allow MSVC to instantiate templates in the code that uses the types, rather than in DLL. Also, I didn't find other boost components that uses DECL macros on types.  
  
Here are my modified version of the `variables_map.hpp` for reference  
```diff  
--- a/boost/program_options/variables_map.hpp     2024-04-08 08:17:53.000000000 +0800  
+++ b/boost/program_options/variables_map.hpp       2025-01-17 12:42:55.994258200 +0800  
@@ -55,7 +55,7 @@  
     /** Class holding value of option. Contains details about how the  
         value is set and allows to conveniently obtain the value.  
     */  
-    class BOOST_PROGRAM_OPTIONS_DECL variable_value {  
+    class variable_value {  
     public:  
         variable_value() : m_defaulted(false) {}  
         variable_value(const boost::any& xv, bool xdefaulted)  
@@ -98,12 +98,12 @@  
         void store(const basic_parsed_options<char>& options,  
               variables_map& m, bool);  
  
-        friend class BOOST_PROGRAM_OPTIONS_DECL variables_map;  
+        friend class variables_map;  
     };  
  
     /** Implements string->string mapping with convenient value casting  
         facilities. */  
-    class BOOST_PROGRAM_OPTIONS_DECL abstract_variables_map {  
+    class abstract_variables_map {  
     public:  
         abstract_variables_map();  
         abstract_variables_map(const abstract_variables_map* next);  
@@ -143,7 +143,7 @@  
         This class is derived from std::map<std::string, variable_value>,  
         so you can use all map operators to examine its content.  
     */  
-    class BOOST_PROGRAM_OPTIONS_DECL variables_map : public abstract_variables_map,  
+    class variables_map : public abstract_variables_map,  
                                public std::map<std::string, variable_value>  
     {  
     public:  
```
