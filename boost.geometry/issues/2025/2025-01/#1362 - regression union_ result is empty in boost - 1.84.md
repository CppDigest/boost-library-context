# #1362 - regression union_ result is empty in boost > 1.84 [Closed]

> Username: jmachowinski  
> Created at: 2025-01-09 13:56:46 UTC  
> Updated at: 2025-02-05 09:49:07 UTC  
> Closed at: 2025-02-05 09:49:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1362  

Union of two overlapping polygons with an 'antenna' is empty in Boost > 1.84  
![Image](https://github.com/user-attachments/assets/7257c426-2170-49d4-b565-9e136b2e81bc)  
  
Reproducer :   
  
https://godbolt.org/z/WEfEvhxzE

---

## Comment 1

> Username: vissarion  
> Created at: 2025-02-05 09:49:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1362#issuecomment-2636240379  

Polygon `b` (the one with the "antenna" or "spike") is not valid according to the [OGC Simple Feature Specification](http://www.opengeospatial.org/standards/sfa) which is implemented in Boost Geometry.   
  
Boost Geometry will not test the input for validity by default, so if an input geometry is not valid the result will be undefined. On the other hand there is a function that can be used to check if your geometry is valid:  
```cpp  
std::string message;  
bool valid = boost::geometry::is_valid(b, message);  
std::cout << "is valid? " << (valid ? "yes" : "no") << std::endl;  
if (! valid)  
{  
    std::cout << "why not valid? " << message << std::endl;  
}  
```  
which also returns exactly why the geometry is not valid,  
```  
is valid? no  
why not valid? Geometry has spikes. A spike point was found with apex at (20, 20)  
```
