# #1131 - Does a Point concept type need N dimension allocation before any usage [Open]

> Username: AndrewLipscomb  
> Created at: 2023-04-17 22:21:09 UTC  
> Updated at: 2023-04-17 22:42:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1131  

I have a specialisation of a `std::vector<double>` derived Point type (for efficient use with GeoJSON) - so as you can expect the backing storage of this class is a `std::vector<double>` which by default construction has 0 elements. The `set` specialisations allow me to resize the `vector` when a dimension's value is requested.  
  
I encountered a bug in my code where an algorithm (`buffer`) was attempting to access the members of an uninitialised point from within the implementation detail of the algorithm.  
  
While its trivial enough for me to initialise my derived type with a N dimension allocation for the default constructor - my question is more _should it_. The concept documentation on https://www.boost.org/doc/libs/1_80_0/libs/geometry/doc/html/geometry/reference/concepts/concept_point.html doesn't appear to mention anything about having the memory backing the N dimensions being allocated on construction - I feel if that is the expected case then that should be documented.   
  
What gives me pause is that this point that was attempted to be accessed did not have any usable data - and might instead indicate a bug with the `buffer` algorithm. In this case - it was attempting to access points on a box derived from a ring in the `buffered_piece_collection` struct that had returned a `strategy::buffer::result_code::result_no_output` - this seems like erroneous behaviour at first glance.

---

## Comment 1

> Username: AndrewLipscomb  
> Created at: 2023-04-17 22:41:25 UTC  
> Updated at: 2023-04-17 22:42:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1131#issuecomment-1512180168  

For reference later once there's an answer above - the uninitialised access comes from the handling of a collapsed simplified ring  
  
```  
        if (code == geometry::strategy::buffer::result_no_output && n >= 1)  
        {  
            // Use point_strategy to buffer degenerated ring  
            detail::buffer::buffer_point<output_point_type>  
                (  
                    geometry::range::front(simplified),  
                    collection, distance, point_strategy  
                );  
        }  
```  
  
This will create a correct value for the ring index in `offsetted_rings` - but `original_rings` remains uninitialised for that ring index . As the return code from this function is `result_no_output` - the initialisation of the `original_ring` for that index which happens in   
```  
    inline void finish_ring(strategy::buffer::result_code code,  
                            InputRing const& input_ring,  
                            bool is_interior, bool has_interiors)  
```  
never happens - but the `original_ring` is still attempted to be accessed later on.
