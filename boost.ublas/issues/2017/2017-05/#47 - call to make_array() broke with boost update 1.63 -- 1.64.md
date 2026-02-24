# #47 - call to make_array() broke with boost update 1.63 -> 1.64 [Open]

> Username: ipuustin  
> Created at: 2017-05-17 20:29:58 UTC  
> Updated at: 2017-05-17 20:44:10 UTC  
> Url: https://github.com/boostorg/ublas/issues/47  

It seems that my viennacl build broke with boost version update 1.63 -> 1.64:  
```  
 In file included from /usr/include/boost/numeric/ublas/vector.hpp:21:0,  
                  from /usr/include/boost/numeric/ublas/matrix.hpp:18,  
                  from /usr/include/boost/numeric/ublas/triangular.hpp:16,  
                  from /build/viennacl-dev-release-1.7.1/examples/tutorial/iterative-ublas.cpp:41:  
 /usr/include/boost/numeric/ublas/storage.hpp: In member function 'void boost::numeric::ublas::unbounded_array<T, ALLOC>::serialize(Archive&, unsigned int)':  
 /usr/include/boost/numeric/ublas/storage.hpp:299:18: error: 'make_array' is not a member of 'boost::serialization'  
              ar & serialization::make_array(data_, s);  
                   ^~~~~~~~~~~~~  
```

---

## Comment 1

> Username: MarcelRaad  
> Created at: 2017-05-17 20:44:10 UTC  
> Url: https://github.com/boostorg/ublas/issues/47#issuecomment-302226053  

I encountered the same issue and created PR https://github.com/uBLAS/ublas/pull/55.
