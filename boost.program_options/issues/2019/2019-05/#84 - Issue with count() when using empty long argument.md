# #84 - Issue with count() when using empty long argument [Closed]

> Username: hrkrshnn  
> Created at: 2019-05-31 12:25:06 UTC  
> Updated at: 2020-12-23 16:02:46 UTC  
> Closed at: 2020-12-23 16:02:46 UTC  
> Url: https://github.com/boostorg/program_options/issues/84  

I'm trying to use the library with an empty long argument and a short argument (in the example: (",h", "help")). The `variable_map.count()` in this case doesn't seem to work, i.e., even if `-h` is an argument count("h") or count(",h") always returns 0. (I'm also not sure what is the correct syntax for count when there is an empty long argument.)  
  
I've added a minimal working example  
  
```cpp  
    #include <iostream>  
    #include <boost/program_options.hpp>  
  
    namespace po = boost::program_options;  
  
    int main(int argc, char* argv[])  
    {  
      po::options_description test("test");  
      test.add_options()  
        (",h", "help");  
  
      po::variables_map vm;  
      po::store(po::parse_command_line(argc, argv, test), vm);  
      po::notify(vm);  
  
      if(vm.count(",h"))  
        {  
          std::cout<<"Success\n"; // never reaches here  
        }  
  
      return 0;  
    }  
```  
  
I had a very quick look at the code, it seems like this isn't currently possible with the library.
