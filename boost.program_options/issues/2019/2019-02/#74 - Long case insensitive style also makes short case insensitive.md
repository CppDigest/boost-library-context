# #74 - Long case insensitive style also makes short case insensitive. [Open]

> Username: Landberg  
> Created at: 2019-02-07 09:25:41 UTC  
> Updated at: 2019-02-07 09:30:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/74  

When you use the  *::long_case_insensitive* style it inadvertently behaves as if you also selected *::short_case_insensitive*.  
  
I noticed this behavior since boost 1.68.0, compiling with 1.67.0 gives the correct result.  
  
In v1.67.0, if you call the test program below like this, `./test -A`, it will give the correct result:  
  
```  
Adam is selected:  false  
Alice is selected: true  
```  
  
In v1.68.0 and v1.69.0 it will give the wrong result, as if *::short_case_insensitive* was also selected.:   
  
```  
Adam is selected:  true  
Alice is selected: false  
```  
  
Basically the *-A* argument becomes an *-a* argument.  
  
## Example code to illustrate the problem.  
  
```  
#include <iostream>  
#include <boost/program_options.hpp>  
  
namespace po = boost::program_options;  
namespace po_style = boost::program_options::command_line_style;  
  
int main(int argc, char* argv[]){  
  
        bool adamSelected = false;  
        bool aliceSelected = false;  
  
  
        try{  
                po::options_description desc("Allowed options");  
                desc.add_options()  
                    ("adam,a", po::value<bool>(&adamSelected)->default_value(false)->implicit_value(true),"Choose Adam")  
                    ("alice,A", po::value<bool>(&aliceSelected)->default_value(false)->implicit_value(true),"Choose Alice")  
                ;  
  
                po::variables_map vm;  
  
                auto parsed = po::command_line_parser(argc, argv).options(desc).style(po_style::unix_style | po_style::long_case_insensitive).run();  
                po::store(parsed, vm);  
  
                po::notify(vm);  
  
                std::cout << "Adam is selected:  " << (adamSelected?"true":"false") << std::endl  
                        << "Alice is selected: " << (aliceSelected?"true":"false") << std::endl;  
        }  
        catch(std::exception& e){  
                std::cerr << e.what() << std::endl;  
        }  
        return 0;  
}  
  
```

---

## Comment 1

> Username: Landberg  
> Created at: 2019-02-07 09:30:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/74#issuecomment-461345857  

I've compiled the test program on Arch Linux x86_64 using g++ v8.2.1 and clang++ v7.01 with the same result.  
I first noticed the problem using Visual Studio 2015 on Windows 10.
