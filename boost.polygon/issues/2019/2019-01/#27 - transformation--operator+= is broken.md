# #27 - transformation::operator+= is broken [Open]

> Username: pkerichang  
> Created at: 2019-01-30 03:34:50 UTC  
> Updated at: 2019-05-05 12:26:07 UTC  
> Url: https://github.com/boostorg/polygon/issues/27  

The concatentation operator of the transformation object is broken.  Simple test code:  
  
```c++  
#include <iostream>  
  
#include <boost/polygon/polygon.hpp>  
  
namespace bp = boost::polygon;  
  
int main() {  
    bp::transformation<int> xform1(bp::axis_transformation::ATR::NULL_TRANSFORM);  
    bp::transformation<int> xform2(bp::axis_transformation::ATR::ROTATE_LEFT);  
  
    bp::point_data<int> shift;  
    xform1.get_translation(shift);  
  
    std::cout << "before: " << shift.x() << ", " << shift.y() << std::endl;  
    xform1 += xform2;  
    xform1.get_translation(shift);  
    std::cout << "after: " << shift.x() << ", " << shift.y() << std::endl;  
}  
```  
The output for me is:  
> before: 0, 0  
> after: 32529, 852967680  
  
Which is wrong, as the "after" values should be (0, 0) as well.  If you look at the implementation in transform.hpp, it's seriously messed up:  
  
```c++  
  // concatenate this with that  
  const transformation& operator+=(const transformation& tr) {  
    coordinate_type x, y;  
    transformation<coordinate_type> inv = inverse();  
    inv.transform(x, y);  
    p_.set(HORIZONTAL, p_.get(HORIZONTAL) + x);  
    p_.set(VERTICAL, p_.get(VERTICAL) + y);  
    // concatenate axis transforms  
    atr_ += tr.atr_;  
    return *this;  
  }  
```  
  
1. variables x and y are uninitialized, leading to garbage values.  
2. the formula makes no sense; the translation of the tr object is not even used.  
  
This seems like such an essential function to be screwed up.  Did it just happened that this operator is not used anywhere else in the project?

---

## Comment 1

> Username: asydorchuk  
> Created at: 2019-05-05 12:25:56 UTC  
> Updated at: 2019-05-05 12:26:07 UTC  
> Url: https://github.com/boostorg/polygon/issues/27#issuecomment-489421755  

Hi @pkerichang, thank you for your report. Yes, it looks messed up. I am the only maintainer of the library at the moment and don't take part in active feature development. Would you be willing to contribute a patch?
