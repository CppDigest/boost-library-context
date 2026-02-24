# #171 - Recommendation about Base Classes for non-polymorphic design [Closed]

> Username: correaa  
> Created at: 2019-08-23 09:29:43 UTC  
> Updated at: 2019-09-20 19:16:10 UTC  
> Closed at: 2019-09-20 19:16:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/171  

The recommendation about base classes in the manual can be misleading , if one is using a strict value-semantics design (I think serialization doesn't make much sense in other designs anyway).  
  
https://www.boost.org/doc/libs/1_70_0/libs/serialization/doc/serialization.html#base  
  
>   
> Resist the temptation to just cast *this to the base class. This might seem to work but may fail to invoke code necessary for proper serialization.    
  
I think there are valid uses for serializing a casted-to-base type, if one is **not** using _polymorphic_ deserialization (into dynamic variables).  
  
Here it is a use case: https://stackoverflow.com/questions/57622684/flatten-boost-serialization-with-named-value-pair/57623315#57623315
