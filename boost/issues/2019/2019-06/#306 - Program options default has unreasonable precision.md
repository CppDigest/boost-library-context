# #306 - Program options default has unreasonable precision [Closed]

> Username: phma  
> Created at: 2019-06-26 04:40:33 UTC  
> Updated at: 2019-06-26 15:45:55 UTC  
> Closed at: 2019-06-26 15:45:55 UTC  
> Url: https://github.com/boostorg/boost/issues/306  

Source code:  
```  int i,e,t,d;  
  time_t now,then;  
  double tolerance,stageTolerance,areadone,rmsadj;  
  bool done=false;  
  string inputFile,outputFile;  
  bool validArgs,validCmd=true;  
  po::options_description generic("Options");  
  po::options_description hidden("Hidden options");  
  po::options_description cmdline_options;  
  po::positional_options_description p;  
  po::variables_map vm;  
  generic.add_options()  
    ("tolerance,t",po::value<double>(&tolerance)->default_value(0.1),"Vertical tolerance")  
    ("output,o",po::value<string>(&outputFile),"Output file");  
  hidden.add_options()  
    ("input",po::value<string>(&inputFile),"Input file");  
  p.add("input",1);  
  cmdline_options.add(generic).add(hidden);  
//...  
      cout<<generic;  
```  
Output:  
```Options:  
  -t [ --tolerance ] arg (=0.10000000000000001)  
                                        Vertical tolerance  
  -o [ --output ] arg                   Output file  
```  
Output should be:  
```Options:  
  -t [ --tolerance ] arg (=0.1)         Vertical tolerance  
  -o [ --output ] arg                   Output file  
```  
I've tried this on Boost 1.65.1 on Ubuntu Beaver, and Boost 1.69.0 on DragonFly BSD 5.5, and got the same result.  
  
For an idea on how to fix it, see ldecimal.cpp in https://github.com/phma/bezitopo, but that would output ".1", not "0.1".

---

## Comment 1

> Username: mjcaisse  
> Created at: 2019-06-26 15:45:55 UTC  
> Url: https://github.com/boostorg/boost/issues/306#issuecomment-505932899  

This ticket would be better placed in the program options repository: https://github.com/boostorg/program_options
