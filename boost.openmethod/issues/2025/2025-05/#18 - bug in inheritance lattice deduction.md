# #18 - bug in inheritance lattice deduction [Closed]

> Username: jll63  
> Created at: 2025-05-10 22:48:53 UTC  
> Updated at: 2025-05-14 00:30:18 UTC  
> Closed at: 2025-05-14 00:30:18 UTC  
> Url: https://github.com/boostorg/openmethod/issues/18  

```c++  
#include <boost/openmethod.hpp>  
#include <boost/openmethod/compiler.hpp>  
  
#include <iostream>  
  
struct Base {  
    virtual ~Base() = default;  
};  
  
struct D1 : Base {};  
struct D2 : D1 {};  
struct D3 : D2 {};  
struct D4 : D3 {};  
struct D5 : D4 {};  
  
BOOST_OPENMETHOD_CLASSES(Base, D1, D2, D3)  
BOOST_OPENMETHOD_CLASSES(D2, D3)  
BOOST_OPENMETHOD_CLASSES(D3, D4)  
BOOST_OPENMETHOD_CLASSES(D4, D5, D3)  
  
auto main() -> int {  
    // Initialize the dispatch tables.  
    boost::openmethod::initialize();  
  
    return 0;  
}  
  
  
// Static class info:  
//     D3: (Base, D1, D2, D3)  
//     D2: (Base, D1, D2)  
//     D1: (Base, D1)  
//     Base: (Base)  
//     D3: (D2, D3)  
//     D2: (D2)  
//     D4: (D3, D4)  
//     D3: (D3)  
//     D3: (D3)  
//     D5: (D5, D3)  
// Inheritance lattice:  
//     D3  
//         bases:      (D2)  
//         derived:    (D4, D5)  
//         covariant: (D4, D5, D3)  
//     D2  
//         bases:      (D1)  
//         derived:    (D3)  
//         covariant: (D3, D5, D4, D2)  
//     D1  
//         bases:      (Base)  
//         derived:    (D2)  
//         covariant: (D2, D4, D5, D3, D1)  
//     Base  
//         bases:      ()  
//         derived:    (D1)  
//         covariant: (D1, D3, D5, D4, D2, Base)  
//     D4  
//         bases:      (D3)  
//         derived:    (D5)  
//         covariant: (D5, D4)  
//     D5  
//         bases:      (D3, D4)  
//         derived:    ()  
//         covariant: (D5)  
```
