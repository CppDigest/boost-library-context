# #694 - Heap corruption caused by boost in c++ [Closed]

> Username: SRAWAS  
> Created at: 2022-10-14 07:49:22 UTC  
> Updated at: 2022-10-20 19:24:53 UTC  
> Closed at: 2022-10-20 19:24:53 UTC  
> Url: https://github.com/boostorg/boost/issues/694  

I have a problem when using boost in c++  
  
I have three projects:  
  
Inheritance (.lib) that contains classes : Foo, Soo, Roo and uti.cpp  
DllInherit (.dll) that contains classes : AA.cpp, FF.cpp, WW.cpp  
execInherits (.exe) that contains main class  
  
Here the code  
  
**Foo.hpp**  
  
 ```  
#pragma once  
 #include <iostream>  
 #include <windows.h>  
 #include <boost/units/base_unit.hpp>  
 #include <boost/units/make_scaled_unit.hpp>  
 #include <boost/units/cmath.hpp>  
 #include <boost/units/systems/si.hpp>  
 #include <boost/units/systems/si/io.hpp>  
 #include <boost/units/systems/si/prefixes.hpp>  
 namespace bu = ::boost::units;  
 namespace si = ::boost::units::si;  
      
 namespace test {  
     using kilowatt_unit = bu::make_scaled_unit<si::power, bu::scale<10, bu::static_rational<3>>>::type;  
     BOOST_UNITS_STATIC_CONSTANT(kilowatt, kilowatt_unit);  
      
     // Masse  
     using ton_unit = bu::make_scaled_unit<si::mass, bu::scale<10, bu::static_rational<3>>>::type;  
     BOOST_UNITS_STATIC_CONSTANT(ton, ton_unit);  
     using PowerCplxQty = bu::quantity<si::power, std::complex<double>>;  
     using MassQty = bu::quantity<si::mass>;  
     using AccelQty = bu::quantity<si::acceleration>;  
     using nlohmann::json;  
     struct PowerData {  
         virtual ~PowerData() {}  
         virtual json toJSON() const = 0;  
         virtual void fromJSON(json const&) = 0;  
     };  
          
     class Foo {  
         std::string m_name;  
     public:  
         Foo(std::string name);  
         virtual ~Foo()   
         {  
             std::cout << "destructor Foo" << std::endl;  
         }  
         std::string const& name() const { return m_name; }  
         virtual std::string power() { return ""; }  
         virtual void create() {}  
         virtual int32_t multiplicity() const { return NULL; }  
         virtual AccelQty deceleration() const { return NULL; }  
         virtual MassQty mass() const { return NULL; }  
         virtual PowerData* powerData() const { return NULL; }  
         virtual bool configure() { return NULL; };  
         __declspec(dllexport) static std::unique_ptr<Foo> generate(std::string name);  
              
     };  
      
 }  
```  
**class Foo.cpp**  
  
```  
#include "Foo.hpp"  
 #define _CRTDBG_MAP_ALLOC  
 #include <cstdlib>  
 #include <crtdbg.h>  
 #include <Windows.h>  
 #include <libloaderapi.h>  
 #include <vld.h>  
 #define LOADER_CODE(path) path.empty() ? GetModuleHandleA(nullptr) : LoadLibraryA(path.c_str())   
      
 #define SYMBOL_CODE(lib, name) reinterpret_cast<void*>(GetProcAddress(static_cast<HMODULE>(lib), name))  
 #define UNLOAD_CODE(lib) FreeLibrary(static_cast<HMODULE>(lib))  
 #define ERROR_CODE GetLastError()  
      
 //Foo::Foo() {  
 //    std::cout << "constructor Foo " << std::endl;  
 //}  
 namespace test {  
     Foo::Foo(std::string name) {  
         m_name = name;  
         std::cout << "name = " << name << std::endl;  
         _CrtDumpMemoryLeaks();  
     }  
      
     __declspec(dllexport) std::unique_ptr<Foo> Foo::generate(std::string name)  
     {  
         std::string path = "DllInherit.dll";  
         std::wstring stemp = std::wstring(path.begin(), path.end());  
         LPCWSTR sw = stemp.c_str();  
         HINSTANCE hinstance = LoadLibrary(sw);  
         auto s1 = (Foo(*)(std::string))SYMBOL_CODE(hinstance, ("create" + name).c_str());  
      
         //   auto s2 = (Foo(*)(std::string))SYMBOL_CODE(hinstance, "createWW");  
      
        std::unique_ptr<Foo> rs = std::unique_ptr<Foo>(new Foo(s1(name)));  
      
        rs->create();  
      
     //    VLDReportLeaks();  
         return rs;  
              
     }  
 }  
```  
  
**class Soo.hpp**  
  
```  
include <iostream>  
include "Foo.hpp"  
define DllExport __declspec( dllexport )  
  
namespace test {  
  
 class Soo : Foo {  
     using Base = Foo;  
     DllExport void create() override;  
     MassQty m_mass;   
 //    AccelQty m_deceleration;  
 //    std::string m_powerUnit = "";  
 //    std::unique_ptr<PowerData> m_powerData;  
 public:  
     using Base::Base;  
     virtual ~Soo() {  
         std::cout << "destructor Soo" << std::endl;  
              
     }  
     DllExport bool configure() override;  
          
     DllExport int32_t multiplicity() const override { return 1; }  
 };}  
```  
  
**class Soo.cpp**  
  
```  
#include "Soo.hpp"  
      
 namespace test {  
      
     void Soo::create() {  
      
     //    m_mass = MassQty(double(data[0]) * ton);  
     //    m_deceleration = double(data[3]) * si::meter_per_second_squared;  
     //    m_powerUnit = data[5];  
     }  
      
     bool Soo::configure() {  
         return true;  
     }  
 }  
```  
 **class Roo.hpp**  
  
 ```  
 #include "Foo.hpp"  
     namespace test {  
         class Roo : public Foo {  
          
         protected:  
             void create() override;  
         public:  
             //    Roo(){}  
             Roo(std::string);  
             virtual ~Roo() {  
                 std::cout << "destructor Roo" << std::endl;  
             }  
             bool configure() override;  
         };  
     }  
```  
  
**class Roo.cpp**  
  
```  
 #include "Roo.hpp"  
 namespace test {  
     Roo::Roo(std::string name) :  
         Foo(name) {  
     }  
     void Roo::create() {  
         std::cout << "create Roo" << std::endl;  
     }  
     bool Roo::configure() {  
         return true;  
     }  
 }  
```  
**class uti.cpp**  
  
```  
 #include "Foo.hpp"  
 namespace test {  
     void insert(std::string name)  
     {  
             auto rs = Foo::generate(name);  
     }  
 }  
```  
**class AA.cpp**  
```  
 #pragma once  
 #include "pch.h"  
 #include "Foo.hpp"  
 #include "Roo.hpp"  
 //#include <vld.h>  
 namespace test {  
     class AA : public Roo {  
         using Base = Roo;  
     public:  
         using Base::Base;  
         void create() override;  
     };  
     void AA::create() {  
         //        VLDEnable();  
         std::cout << "create AA " << std::endl;  
         Base::create();  
         _CrtDumpMemoryLeaks();  
         //        VLDReportLeaks();  
     }  
     extern "C" __declspec(dllexport) AA createAA(std::string name)  
     {  
         return AA(name);  
     }  
 }  
```  
**class WW.cpp**  
  
```  
 #include "pch.h"  
 #include "Soo.hpp"  
      
 namespace test {  
     class WW : public Soo {  
         using Base = Soo;  
          
     //    std::string m_powerUnit;  
     public:  
         using Base::Base;  
      
         //    void create() {  
                 //        VLDEnable();  
         //        std::cout << "create WW " << std::endl;  
         //        Base::create();  
         //        _CrtDumpMemoryLeaks();  
                 //        VLDReportLeaks();  
         //    }  
     };  
      
     extern "C" __declspec(dllexport) WW createWW(std::string name)  
     {  
         return WW(name);  
     }  
      
 }  
```  
  
**class FF.cpp**  
```  
  
#include "pch.h"  
 #include "Foo.hpp"  
 namespace test {  
     class FF : public Foo {  
         using Base = Foo;  
         MassQty m_mass;  
         AccelQty m_deceleration;  
         std::string static const m_powerUnit;  
         void create() override;  
      
     public:  
         using Base::Base;  
     };  
      
     extern "C" __declspec(dllexport) FF createFF(std::string name)  
     {  
         return FF(name);  
     }  
      
     void FF::create() {  
              
     }  
 }  
```  
  
**class main.cpp**  
```  
  
 #include <Windows.h>  
 #include <iostream>  
 #define _CRTDBG_MAP_ALLOC  
 #include <crtdbg.h>  
 #include <stdlib.h>  
      
 #include "Foo.hpp"  
 #include <boost/filesystem.hpp>  
 #include <boost/program_options.hpp>  
 #include <boost/filesystem/fstream.hpp>  
 #include "Roo.hpp"  
      
 #include <vld.h>  
 #include <libloaderapi.h>  
 #include <errhandlingapi.h>  
 #include <winbase.h>   
 #include "Uti.cpp"  
 #include <nlohmann/json.hpp>  
 #include "Foo.hpp"  
      
 using nlohmann::json;  
 namespace fs = boost::filesystem;  
 namespace po = boost::program_options;  
      
      
 fs::path getPath(po::variables_map const& vm, char const* name, bool optional) {  
      
     fs::path path;  
      
     if (vm.count(name)) {  
         path = vm[name].as<std::string>();  
         if (path.is_relative())  
             path = fs::current_path() / path;  
     }  
      
     return path;  
 }  
      
 int main(int argc, char** argv)  
 {  
    test::insert("WW");  
     std::cout << "Hello World!\n";  
  //   _CrtDumpMemoryLeaks();  
    return 0;  
 }  
```  
  
I have the error : "HEAP CORRUPTION DETECTED: after Normal block (#1542) at 0x000001FDE39A5950.  
CRT detected that the application wrote to memory after end of heap buffer " at the end of scope of destructor of Soo.  
  
Any suggestions please !
