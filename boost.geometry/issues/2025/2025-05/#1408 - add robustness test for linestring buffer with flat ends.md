# #1408 - add robustness test for linestring buffer with flat ends [Open]

> Username: barendgehrels  
> Created at: 2025-05-13 19:44:25 UTC  
> Updated at: 2025-05-13 19:44:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1408  

See #1404   
It should specifically hunt for and test, in `include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp`  
```  
        if (piece.type == geometry::strategy::buffer::buffered_empty_side)  
        {  
            return false;  
```  
  
and what would go wrong there in some cases
