# #51 - crash under python3.5 [Open]

> Username: ghost  
> Created at: 2015-12-31 07:38:14 UTC  
> Updated at: 2016-07-07 13:54:32 UTC  
> Url: https://github.com/boostorg/python/issues/51  

# include <boost/python.hpp>  
  
using namespace boost::python;  
int main()  
{  
    Py_Initialize();  
    object main_module = import("**main**");  
}  
  
There is an exception:  
  
Exception thrown at 0x00000000004E126A (python35.dll) in test.exe: 0xC0000005: Access violation reading location 0x0000000000000025.  
  
Call stack:  
  
python35.dll!PyUnicode_InternInPlace(_object \* \* p) Line 15007 C python35.dll!PyImport_Import(_object \* module_name) Line 1752 C python35.dll!PyImport_ImportModule(const char \* name) Line 1260 C test.exe!boost::python::import(boost::python::str name) Line 20 C++  
  
I try static link or dynamic link, all the same. I use vs2015, python 3.5.1, x64 build, Unicode set

---

## Comment 1

> Username: nasirk49  
> Created at: 2016-07-07 13:54:32 UTC  
> Url: https://github.com/boostorg/python/issues/51#issuecomment-231084337  

I have exactly the same issue.
