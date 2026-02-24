# #44 Added missing array_wrapper.hpp [Closed]

> Username: oxidase  
> Created at: 2016-11-20 12:19:45 UTC  
> Updated at: 2016-12-29 23:01:05 UTC  
> Closed at: 2016-12-29 23:01:05 UTC  
> Url: https://github.com/boostorg/ublas/pull/44  

related commit https://github.com/boostorg/serialization/commit/6b33d1cd4e11daaf97612561ecd9d4848843897c  
leads for boost/geometry.hpp to an error  
```  
/home/miha/foss/boost/boost/numeric/ublas/storage.hpp: In member function ‘void boost::numeric::ublas::unbounded_array<T, ALLOC>::serialize(Archive&, unsigned int)’:  
/home/miha/foss/boost/boost/numeric/ublas/storage.hpp:331:18: error: ‘make_array’ is not a member of ‘boost::serialization’  
             ar & serialization::make_array(data_, s);  
                  ^~~~~~~~~~~~~  
```

---

## Comment 1

> Username: oxidase  
> Created_at: 2016-12-29 23:01:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/44#issuecomment-269707113  

Superseded by #46

---
