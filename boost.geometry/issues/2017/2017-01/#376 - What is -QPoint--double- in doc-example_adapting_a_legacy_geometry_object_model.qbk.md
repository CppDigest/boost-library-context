# #376 - What is "QPoint::double" in doc/example_adapting_a_legacy_geometry_object_model.qbk ? [Closed]

> Username: cirosantilli  
> Created at: 2017-01-09 14:55:34 UTC  
> Updated at: 2017-01-10 14:38:22 UTC  
> Closed at: 2017-01-09 17:10:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/376  

https://github.com/boostorg/geometry/blob/14d9d32d04349a518e3ac0dd237a2a81f6eceaa9/doc/example_adapting_a_legacy_geometry_object_model.qbk#L125  
  
In particular, fails to compile with the definition: https://github.com/boostorg/geometry/blob/14d9d32d04349a518e3ac0dd237a2a81f6eceaa9/doc/example_adapting_a_legacy_geometry_object_model.qbk#L17  
  
Removing the `QPoint::` part works.  
  
Sorry if a C++ / library feature that Google could not explain to me.

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-01-09 17:10:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/376#issuecomment-271343560  

Yes, it's probably a mistake in the example. I guess that original author at the beginning defined some coordinate type typedef in QPoint and at some point automatically changed the name to double. I'll fix it, thanks!

---

## Comment 2

> Username: mloskot  
> Created at: 2017-01-09 18:05:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/376#issuecomment-271358233  

For the record, I guess here is the original `QPoint` adoption:  
https://gist.github.com/anonymous/1798839 (with variant here https://gist.github.com/meastp/1852693)  
developed by @meastp  as part of this thread  
http://boost-geometry.203548.n3.nabble.com/Using-Boost-Geometry-with-a-legacy-class-hierarchy-td3734832.html  
  
That initial version includes  
  
```  
template<> struct coordinate_type<QPoint>  
{ typedef double type; };  
```

---

## Comment 3

> Username: meastp  
> Created at: 2017-01-10 05:51:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/376#issuecomment-271492700  

@cirosantilli, @awulkiew : It is a typo, and should be just double instead of QPoint::double. :)
