# #579 - -Wpessimizing-move in min_max [Closed]

> Username: mborland  
> Created at: 2023-12-19 09:38:50 UTC  
> Updated at: 2023-12-22 13:05:26 UTC  
> Closed at: 2023-12-22 13:05:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/579  

```  
./boost/multiprecision/detail/min_max.hpp:100:26: error: moving a local object in a return statement prevents copy elision [-Werror=pessimizing-move]  
  100 |       return std::move(t1);  
      |                          ^  
./boost/multiprecision/detail/min_max.hpp:100:26: note: remove ‘std::move’ call  
./boost/multiprecision/detail/min_max.hpp:101:23: error: moving a local object in a return statement prevents copy elision [-Werror=pessimizing-move]  
  101 |    return std::move(t2);  
      |                       ^  
./boost/multiprecision/detail/min_max.hpp:101:23: note: remove ‘std::move’ call  
cc1plus: all warnings being treated as errors  
```  
  
Found while working on odeint here: https://github.com/boostorg/odeint/actions/runs/7259531925/job/19776870666?pr=66#step:10:4092
