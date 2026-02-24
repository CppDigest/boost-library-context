# #1001 - Mirroring geometries [Closed]

> Username: xakod  
> Created at: 2022-05-16 07:36:54 UTC  
> Updated at: 2022-05-16 09:57:50 UTC  
> Closed at: 2022-05-16 09:57:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1001  

Does boost::geometry supports something for mirroring geometries out-of-the-box? I cannot find something releted

---

## Comment 1

> Username: tinko92  
> Created at: 2022-05-16 08:46:25 UTC  
> Updated at: 2022-05-16 08:50:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1001#issuecomment-1127398989  

The algorithm boost::geometry::transform with a scale-strategy with a negative value for one of the dimensions might be what you are looking for.  
  
```cpp  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
int main()  
{  
    using namespace boost::geometry;  
    using namespace boost::geometry::strategy::transform;  
    using point_2d = model::d2::point_xy<double>;  
      
    point_2d p(2,5), pm;  
    scale_transformer<double, 2, 2> mirror(-1, 1);  
    transform(p, pm, mirror);  
    std::cout << wkt(pm) << "\n"; //POINT(-2 5)  
  
    return 0;  
}  
```  
  
More generally, with matrix_transformer you can have arbitrary affine transformations, this example shows how they can be used and chained: https://github.com/boostorg/geometry/blob/develop/example/06_b_transformation_example.cpp . Here you can see the definitions of various matrix transformers: https://github.com/boostorg/geometry/blob/6235b8ae78ac0699280c5418aaf1e58f4141f451/include/boost/geometry/strategies/transform/matrix_transformers.hpp#L168 .
