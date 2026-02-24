# #865 - R-tree serializer false error [Closed]

> Username: maliberty  
> Created at: 2021-06-11 03:21:59 UTC  
> Updated at: 2021-06-18 09:52:42 UTC  
> Closed at: 2021-06-18 09:52:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/865  

In the attached program I try to serialize/deserialize a tree with a single element.  It fails on deserialize in:  
  
        if ( elements_count < parameters.get_min_elements() || parameters.get_max_elements() < elements_count )  
            BOOST_THROW_EXCEPTION(std::runtime_error("rtree loading error"));  
  
since there is just one element the root node must violate the min_elements rule (min of 4 in this case).  This shouldn't be an error.   I expect this is easy to fix but I don't know the internals.    
[test.cpp.gz](https://github.com/boostorg/geometry/files/6635560/test.cpp.gz)

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-06-11 09:13:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/865#issuecomment-859416851  

Thanks for the report! Yes, root leaf node should be able to hold smaller number of elements than the minimum.
