# #26 - boost/icl/concept/interval.hpp: error: unused typedef domain_compare & domain_type [Open]

> Username: k15tfu  
> Created at: 2019-10-15 11:53:21 UTC  
> Updated at: 2023-10-18 07:50:19 UTC  
> Url: https://github.com/boostorg/icl/issues/26  

Hi! Clang 9.0 generates the following warnings:  
  
```  
In file included from .../include/boost/icl/interval_map.hpp:13:  
In file included from .../include/boost/icl/interval_set.hpp:14:  
In file included from .../include/boost/icl/interval_base_set.hpp:24:  
In file included from .../include/boost/icl/associative_interval_container.hpp:13:  
In file included from .../include/boost/icl/concept/joinable.hpp:12:  
.../include/boost/icl/concept/interval.hpp:111:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:110:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:124:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:123:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:179:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:180:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:197:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:196:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:292:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:325:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:424:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:423:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:436:60: error: unused typedef 'domain_compare' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_compare domain_compare;  
                                                           ^  
.../include/boost/icl/concept/interval.hpp:435:60: error: unused typedef 'domain_type' [-Werror,-Wunused-local-typedef]  
    typedef typename interval_traits<Type>::domain_type    domain_type;  
                                                           ^  
14 errors generated.  
```

---

## Comment 1

> Username: liuqikang  
> Created at: 2020-12-09 02:52:43 UTC  
> Url: https://github.com/boostorg/icl/issues/26#issuecomment-741492164  

is it solved?

---

## Comment 2

> Username: PeterFeicht  
> Created at: 2023-10-18 07:50:19 UTC  
> Url: https://github.com/boostorg/icl/issues/26#issuecomment-1767881169  

Seemingly fixed in PR #38, which is not yet released.
