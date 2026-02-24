# #68 - options cannot be stored in variables [Open]

> Username: Caldfir  
> Created at: 2018-11-25 20:58:32 UTC  
> Updated at: 2019-11-19 03:39:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/68  

It seems like storing options in variables doesn't work.  
  
Sample code (based on tutorial):  
```C++  
#include <boost/program_options.hpp>  
using namespace boost::program_options;  
  
#include <iostream>  
using namespace std;  
  
/* Auxiliary functions for checking input for validity. */  
  
/* Function used to check that 'opt1' and 'opt2' are not specified  
at the same time. */  
void conflicting_options(const variables_map& vm,  
  const char* opt1, const char* opt2)  
{  
  if (vm.count(opt1) && !vm[opt1].defaulted()  
    && vm.count(opt2) && !vm[opt2].defaulted())  
    throw logic_error(string("Conflicting options '")  
      + opt1 + "' and '" + opt2 + "'.");  
}  
  
/* Function used to check that of 'for_what' is specified, then  
'required_option' is specified too. */  
void option_dependency(const variables_map& vm,  
  const char* for_what, const char* required_option)  
{  
  if (vm.count(for_what) && !vm[for_what].defaulted())  
    if (vm.count(required_option) == 0 || vm[required_option].defaulted())  
      throw logic_error(string("Option '") + for_what  
        + "' requires option '" + required_option + "'.");  
}  
  
int main(int argc, char* argv[])  
{  
  try {  
    string ofile;  
    string macrofile, libmakfile;  
    bool t_given = false;  
    bool b_given = false;  
    string mainpackage;  
    string depends = "deps_file";  
    string sources = "src_file";  
    string root = ".";  
  
    options_description desc("Allowed options");  
    desc.add_options()  
      // First parameter describes option name/short name  
      // The second is parameter to option  
      // The third is description  
      ("help,h", "print usage message")  
      ("output,o", value(&ofile), "pathname for output")  
      ("macrofile,m", value(&macrofile), "full pathname of macro.h")  
      ("two,t", bool_switch(&t_given), "preprocess both header and body")  
      ("body,b", bool_switch(&b_given), "preprocess body in the header context")  
      ("libmakfile,l", value(&libmakfile),  
        "write include makefile for library")  
        ("mainpackage,p", value(&mainpackage),  
          "output dependency information")  
          ("depends,d", value(&depends),  
            "write dependencies to <pathname>")  
            ("sources,s", value(&sources), "write source package list to <pathname>")  
      ("root,r", value(&root), "treat <dirname> as project root directory")  
      ;  
  
    variables_map vm;  
    store(parse_command_line(argc, argv, desc), vm);  
  
    if (vm.count("help")) {  
      cout << desc << "\n";  
      return 0;  
    }  
  
    conflicting_options(vm, "output", "two");  
    conflicting_options(vm, "output", "body");  
    conflicting_options(vm, "output", "mainpackage");  
    conflicting_options(vm, "two", "mainpackage");  
    conflicting_options(vm, "body", "mainpackage");  
  
    conflicting_options(vm, "two", "body");  
    conflicting_options(vm, "libmakfile", "mainpackage");  
    conflicting_options(vm, "libmakfile", "mainpackage");  
  
    option_dependency(vm, "depends", "mainpackage");  
    option_dependency(vm, "sources", "mainpackage");  
    option_dependency(vm, "root", "mainpackage");  
  
    cout << "two = " << vm["two"].as<bool>() << "\n";  
    cout << "depends = " << depends << "\n";  
    cout << "depends = " << vm["depends"].as<string>() << "\n";  
  }  
  catch (exception& e) {  
    cerr << e.what() << "\n";  
  }  
}  
```  
  
And here is an example console printout:  
```  
$ ./Gmame.exe -p e -d fg  
two = 0  
depends = deps_file  
depends = fg  
```  
  
It seems like the provided storage variable is just being ignored? If this is incorrect somehow then the tutorial needs to be updated as well.  
  
Building using boost 1.68 on Windows using MSVC 2017.

---

## Comment 1

> Username: DavidAntliff  
> Created at: 2019-11-19 02:44:53 UTC  
> Updated at: 2019-11-19 03:39:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/68#issuecomment-555306971  

I also see this with 1.69 on Ubuntu Linux with gcc 7.4.0. I can access the values via `vm["option_name"].as<T>()` however.  
  
OK, the problem is that both @Caldfir and myself are forgetting to call `po::notify(vm)` after the call(s) to `po::store()`. Once I add this, the variables are populated correctly.
