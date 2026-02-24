# #87 Compile the SGB examples. [Merged]

> Username: ascherer  
> Created at: 2017-03-19 14:36:16 UTC  
> Updated at: 2017-05-27 00:00:46 UTC  
> Merged at: 2017-05-27 00:00:45 UTC  
> Closed at: 2017-05-27 00:00:45 UTC  
> Url: https://github.com/boostorg/graph/pull/87  

System: (K)Ubuntu 16.04 LTS  
Compiler: g++ (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609  
  
Try to `g++ -c --std=c++14` the four BGL–SGB examples `girth.cpp`, `miles_span.cpp`, `roget_components.cpp`, and `topo-sort-with-sgb.cpp`. This leads to numerous errors.  
  
## Applied fixes  
  
1. **SGB 2002-01-30** changed the “Associated Press scores” from `ap0` and `ap1` to `ap`. This preprocessor macro (sic!) collides with numerous function parameters in `named_function_params.hpp`. `#undef`ining the macro doesn't break the BGL–SGB examples.  
  
2. `girth.cpp` fails because of a missing `null_vertex()` function. Added to the header templates.  
  
3. `roget_components.cpp` invokes `strncmp()` from the C library. `#include` added.  
  
4. `topo-sort-with-sgb.cpp` hickups because of a missing `vertices()` function. Following `roget_components.cpp` and putting the collective SGB header up front fixes this.  
  
## Results  
  
With my local installation of the [Stanford GraphBase](https://github.com/ascherer/sgb/tree/local), all four **example** programs compile and link fine with the commandline (from the `example/` subdirectory)  
```  
for example in girth miles_span roget_components topo-sort-with-sgb  
do  
    g++ --std=c++14 -I../include -I/usr/include/sgb $example.cpp -o $example -lgb -L/usr/lib/sgb  
done  
```  
The resulting executables produce the expected outputs.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2017-05-26 19:55:58 UTC  
> Url: https://github.com/boostorg/graph/pull/87#issuecomment-304374606  

Graph doesn't support C++14 at this time.  We have numerous developers who still need C++98 so we're slowing moving forward to C++11.  We're looking for migration paths to newer compilers without losing legacy codes and developers, finding the right balance is difficult.

---

## Comment 2

> Username: ascherer  
> Created_at: 2017-05-26 23:37:35 UTC  
> Url: https://github.com/boostorg/graph/pull/87#issuecomment-304409086  

Well, the problems already occur without the `--std=c++14` option and can be fixed for older compilers just the same way.

---
