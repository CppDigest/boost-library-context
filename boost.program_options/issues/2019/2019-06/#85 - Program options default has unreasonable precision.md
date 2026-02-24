# #85 - Program options default has unreasonable precision [Open]

> Username: phma  
> Created at: 2019-06-28 04:17:37 UTC  
> Updated at: 2022-09-03 21:55:59 UTC  
> Url: https://github.com/boostorg/program_options/issues/85  

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

> Username: e-kwsm  
> Created at: 2019-11-15 14:11:44 UTC  
> Url: https://github.com/boostorg/program_options/issues/85#issuecomment-554373288  

```diff  
-    ("tolerance,t",po::value<double>(&tolerance)->default_value(0.1),"Vertical tolerance")  
+    ("tolerance,t",po::value<double>(&tolerance)->default_value(0.1, "0.1"),"Vertical tolerance")  
```

---

## Comment 2

> Username: mattgruenke  
> Created at: 2022-09-03 21:55:59 UTC  
> Url: https://github.com/boostorg/program_options/issues/85#issuecomment-1236203953  

> ```diff  
> -    ("tolerance,t",po::value<double>(&tolerance)->default_value(0.1),"Vertical tolerance")  
> +    ("tolerance,t",po::value<double>(&tolerance)->default_value(0.1, "0.1"),"Vertical tolerance")  
> ```  
  
Thanks for mentioning that, but it adds verbosity (and quite a lot, if you want to do it in a way that avoids redundancy) when the default precision really should be lower.  
  
Further, I wonder if a better solution wouldn't be to have an overload of `default_value()` that takes an arbitrary iomanip.  That would enable the entire range of formatting customizations that `std::ostream` supports.
