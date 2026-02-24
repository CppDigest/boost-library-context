# #122 - Trouble with std::shared_ptr exposing custom inherited classes [Closed]

> Username: wayne-chen  
> Created at: 2017-03-31 04:47:17 UTC  
> Updated at: 2017-04-04 17:46:38 UTC  
> Closed at: 2017-04-04 17:46:38 UTC  
> Url: https://github.com/boostorg/python/issues/122  

Hi Boost Python maintainers,  
  
I'm unable to create a std::shared_ptr for a derived class that is called from a function in a manager class.    
  
There's a [Stack Overflow](https://stackoverflow.com/questions/32182346/error-c2027-use-of-undefined-type-boostpythondetailreference-existing-ob) post that I followed with the example in the solution that I tried to see if it would work, and it compiled with no issues.  However, I'm not able to reproduce the same results with my scenario, as the class declaration of `Derived` is located in another header file.  
  
My code can be found zipped here:   
[TestingBoostPython.zip](https://github.com/boostorg/python/files/884552/TestingBoostPython.zip)  
  
I receive this error when compiling the Visual Studio .sln:  
`Error	LNK2019:	unresolved external symbol "class Derived const volatile * __cdecl boost::get_pointer<class Derived const volatile >(class Derived const volatile *)" (??$get_pointer@$$CDVDerived@@@boost@@YAPEDVDerived@@PEDV1@@Z) referenced in function "public: static struct _object * __cdecl boost::python::objects::make_instance_impl<class Derived,struct boost::python::objects::pointer_holder<class std::shared_ptr<class Derived>,class Derived>,struct boost::python::objects::make_ptr_instance<class Derived,struct boost::python::objects::pointer_holder<class std::shared_ptr<class Derived>,class Derived> > >::execute<class std::shared_ptr<class Derived> >(class std::shared_ptr<class Derived> &)" (??$execute@V?$shared_ptr@VDerived@@@std@@@?$make_instance_impl@VDerived@@U?$pointer_holder@V?$shared_ptr@VDerived@@@std@@VDerived@@@objects@python@boost@@U?$make_ptr_instance@VDerived@@U?$pointer_holder@V?$shared_ptr@VDerived@@@std@@VDerived@@@objects@python@boost@@@345@@objects@python@boost@@SAPEAU_object@@AEAV?$shared_ptr@VDerived@@@std@@@Z)`  
  
Directions for setting up:  
-Open a `cmd.exe` instance.  
-cd to `/path/to/TestingBoostPython/directory`.  
-`mkdir build`  
-`chdir build`  
-`cmake .. -G "Visual Studio 14 2015 Win64"`.  
-Close `cmd.exe` and navigate to the build folder to open the .sln in Visual Studio,  
  
My build environment is: Windows 7, Boost 1.63.0, CMake 3.7.2, Microsoft Visual Studio 2015 x64.  
  
BOOST_ROOT and BOOST_LIBRARYDIR paths are already set up in my environment variables.  
  
Any help/advice would be greatly appreciated for this.  Thanks.

---

## Comment 1

> Username: wayne-chen  
> Created at: 2017-04-04 17:46:37 UTC  
> Url: https://github.com/boostorg/python/issues/122#issuecomment-291578252  

Solved using this (Stack Overflow)[http://stackoverflow.com/questions/38261530/unresolved-external-symbols-since-visual-studio-2015-update-3-boost-python-link] post and using the `register_ptr_to_python<std::shared_ptr<Derived>>();` declaration.
