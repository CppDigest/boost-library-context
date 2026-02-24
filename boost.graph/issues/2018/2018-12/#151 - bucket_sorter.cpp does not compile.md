# #151 - bucket_sorter.cpp does not compile [Closed]

> Username: jzmaddock  
> Created at: 2018-12-16 19:14:08 UTC  
> Updated at: 2026-02-21 21:31:46 UTC  
> Closed at: 2026-02-21 21:31:46 UTC  
> Url: https://github.com/boostorg/graph/issues/151  

```  
1>bucket_sorter.cpp  
1>d:\data\boost\boost\libs\graph\example\bucket_sorter.cpp(46): warning C4267: 'argument': conversion from 'size_t' to 'const int', possible loss of data  
1>d:\data\boost\boost\libs\graph\example\bucket_sorter.cpp(65): warning C4267: 'argument': conversion from 'size_t' to 'const int', possible loss of data  
1>d:\data\boost\boost\boost\pending\bucket_sorter.hpp(58): error C3861: 'get': identifier not found  
1>d:\data\boost\boost\boost\pending\bucket_sorter.hpp(58): note: 'get': function was not declared in the template definition context and can be found only via argument-dependent lookup in the instantiation context  
1>d:\data\boost\boost\boost\pending\bucket_sorter.hpp(57): note: while compiling class template member function 'void boost::bucket_sorter<size_t,int,std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,ID>::push(const int &)'  
1>        with  
1>        [  
1>            _Ty=size_t  
1>        ]  
1>d:\data\boost\boost\libs\graph\example\bucket_sorter.cpp(46): note: see reference to function template instantiation 'void boost::bucket_sorter<size_t,int,std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,ID>::push(const int &)' being compiled  
1>        with  
1>        [  
1>            _Ty=size_t  
1>        ]  
1>d:\data\boost\boost\libs\graph\example\bucket_sorter.cpp(43): note: see reference to class template instantiation 'boost::bucket_sorter<size_t,int,std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,ID>' being compiled  
1>        with  
1>        [  
1>            _Ty=size_t  
1>        ]  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-12-18 18:02:45 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-448313454  

@anadon : I've just filed a bunch of bug reports against examples that appear broken, and that I don't have time to debug/fix.  Would you be able to take a look at any of these?  
Hopefully not a "hospital pass"!

---

## Comment 2

> Username: deinst  
> Created at: 2019-01-20 02:24:30 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-455831880  

The problem here is that bucket_sorter expects a property map as the `ValueIndexMap`.  The code compiles and seems to work if we change the line  
  
    typedef trivial_id ID;  
  
to  
  
    typedef boost::identity_property_map ID;  
  
and add an include for property_map.  
  
I'm not sure exactly what the interface should be.  Is there any documentation?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-01-20 11:21:17 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-455857717  

>I'm not sure exactly what the interface should be. Is there any documentation?  
  
Ugh, I suspect not - it's an example for boost/pending/bucket_sorter.hpp which is really an undocumented detail (albeit a possibly useful one).  
  
So I guess we can either:  
  
1) Just remove it - seems a shame?  
2) Fix as you suggested above, and assuming it now builds and runs OK, just shrug and move on I guess.  
  
Question: should it be boost/pending/bucket_sorter.hpp which adds that #include if a property map is a requirement of it's use?

---

## Comment 4

> Username: deinst  
> Created at: 2019-01-20 22:55:36 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-455910563  

> Question: should it be boost/pending/bucket_sorter.hpp which adds that #include if a property map > is a requirement of it's use?  
  
As property map is a concept, not a type per se, I don't know if this is necessary.  Things should work smoothly with any type that .  We could add a       
  
    BOOST_CONCEPT_ASSERT ((ReadablePropertyMap<ValueIndexMap, ValueType>));  
  
line to bucket_sorter.hpp  (or something similar, I'm not yet comfortable with the boost concept macros).  
  
At a higher level this class is more of a bucket_vector than a bucket_sorter as no sorting is done at all.  It maintains a vector of buckets, nothing more. I suspect that this was an abandoned attempt at what later became boost/graph/planar_detail/bucket_sort.hpp.  
  
  
I would vote to just remove it unfinished.  We could call it bucket list first :)

---

## Comment 5

> Username: anadon  
> Created at: 2019-04-05 20:44:05 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-480415967  

@deinst Thanks!

---

## Comment 6

> Username: hdu-sdlzx  
> Created at: 2022-12-10 02:41:06 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-1344988006  

Why not close this issue? @deinst @anadon @jzmaddock

---

## Comment 7

> Username: andreacassioli  
> Created at: 2026-02-21 13:46:16 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-3938803809  

Another issue to close @jeremy-murphy (or who else can?)

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2026-02-21 21:31:39 UTC  
> Url: https://github.com/boostorg/graph/issues/151#issuecomment-3939538911  

Thanks, @andreacassioli .
