# #38 fix unused typedef in release mode [Merged]

> Username: alexiprof  
> Created at: 2022-11-10 11:45:58 UTC  
> Updated at: 2023-05-09 15:18:37 UTC  
> Merged at: 2023-05-09 15:18:37 UTC  
> Closed at: 2023-05-09 15:18:37 UTC  
> Url: https://github.com/boostorg/icl/pull/38  

fix unused typedef in release mode  
  
fix this error in release build  
```cpp  
In file included from ./include/boost/icl/continuous_interval.hpp:15:  
./include/boost/icl/concept/interval.hpp:112:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:111:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:125:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:124:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:181:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:180:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:198:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:197:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:293:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:326:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:424:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:425:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:436:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
./include/boost/icl/concept/interval.hpp:437:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
14 errors generated.  
```

---
