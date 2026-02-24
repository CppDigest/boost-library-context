# #635 - Catmull-Rom Spline: () Operator Index Out of Bounds When Using max_parameter() as "s" and Closed Set to True [Closed]

> Username: TevansMusco  
> Created at: 2021-05-26 19:36:48 UTC  
> Updated at: 2021-05-27 17:32:57 UTC  
> Closed at: 2021-05-27 17:32:57 UTC  
> Url: https://github.com/boostorg/math/issues/635  

I have run into an issue when using the () operator in the catmull_rom class. Here is a code snippet that generates the issue:  
```  
std::vector<Vector3f> pts; // Setup list of points  
pts.push_back(Vector3f(-5.0f, -5.0f, 0.0f));  
pts.push_back(Vector3f(5.0f, -5.0f, 0.0f));  
pts.push_back(Vector3f(5.0f, 5.0f, 0.0f));  
pts.push_back(Vector3f(-5.0f, 5.0f, 0.0f));  
  
boost::math::catmull_rom<Vector3f> spline(std::move(pts), true, 1.0); // Setting closed to true triggers the error  
Vector3f f = spline(spline.max_parameter()); // Index out of bounds for m_s on line 163 of catmull_rom.hpp  
```  
To briefly describe, when I create a closed spline and try to retrieve the coordinates of the last point on the spline using max_parameter and the () operator, there is an index out of bounds error on line 163 of catmull_rom.hpp.  
  
Any help on either addressing the issue or explaining why this would happen would be greatly appreciated. Thanks!

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-05-26 19:59:51 UTC  
> Url: https://github.com/boostorg/math/issues/635#issuecomment-849078083  

Reproduced.  
  
```cpp  
#include <iostream>  
#include <boost/math/interpolators/catmull_rom.hpp>  
#include <array>  
  
using Vector3f = std::array<float, 3>;  
  
int main() {  
  
    std::vector<Vector3f> pts; // Setup list of points  
    pts.push_back({-5.0f, -5.0f, 0.0f});  
    pts.push_back({5.0f, -5.0f, 0.0f});  
    pts.push_back({5.0f, 5.0f, 0.0f});  
    pts.push_back({-5.0f, 5.0f, 0.0f});  
  
    boost::math::catmull_rom<Vector3f> spline(std::move(pts), true, 1.0); // Setting closed to true triggers the error  
    Vector3f f = spline(spline.max_parameter()); // Index out of bounds for m_s on line 163 of catmull_rom.hpp  
  
}  
```  
  
Compile with:  
  
```  
$ g++ -fsanitize=address -fsanitize=undefined --std=c++14 -I./include reproduce.cpp  
$ ./a.out  
=================================================================  
==41384==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x6030000019ac at pc 0x00010d240a2c bp 0x7ffee29c1dd0 sp 0x7ffee29c1dc8  
READ of size 4 at 0x6030000019ac thread T0  
    #0 0x10d240a2b in boost::math::catmull_rom<std::__1::array<float, 3ul>, std::__1::vector<std::__1::array<float, 3ul>, std::__1::allocator<std::__1::array<float, 3ul> > > >::operator()(float) const (a.out:x86_64+0x100003a2b)  
    #1 0x10d23ee42 in main (a.out:x86_64+0x100001e42)  
    #2 0x7fff20414f5c in start (libdyld.dylib:x86_64+0x15f5c)  
  
0x6030000019ac is located 0 bytes to the right of 28-byte region [0x603000001990,0x6030000019ac)  
allocated by thread T0 here:  
    #0 0x10d2e4502 in wrap__Znwm (libclang_rt.asan_osx_dynamic.dylib:x86_64h+0x6e502)  
    #1 0x10d249490 in std::__1::__libcpp_allocate(unsigned long, unsigned long) (a.out:x86_64+0x10000c490)  
    #2 0x10d251707 in std::__1::allocator<float>::allocate(unsigned long, void const*) (a.out:x86_64+0x100014707)  
    #3 0x10d2511d8 in std::__1::allocator_traits<std::__1::allocator<float> >::allocate(std::__1::allocator<float>&, unsigned long) (a.out:x86_64+0x1000141d8)  
    #4 0x10d250e39 in std::__1::__split_buffer<float, std::__1::allocator<float>&>::__split_buffer(unsigned long, unsigned long, std::__1::allocator<float>&) (a.out:x86_64+0x100013e39)  
    #5 0x10d24efe8 in std::__1::__split_buffer<float, std::__1::allocator<float>&>::__split_buffer(unsigned long, unsigned long, std::__1::allocator<float>&) (a.out:x86_64+0x100011fe8)  
    #6 0x10d24e3b9 in std::__1::vector<float, std::__1::allocator<float> >::__append(unsigned long) (a.out:x86_64+0x1000113b9)  
    #7 0x10d24c1b8 in std::__1::vector<float, std::__1::allocator<float> >::resize(unsigned long) (a.out:x86_64+0x10000f1b8)  
    #8 0x10d24ac38 in boost::math::catmull_rom<std::__1::array<float, 3ul>, std::__1::vector<std::__1::array<float, 3ul>, std::__1::allocator<std::__1::array<float, 3ul> > > >::catmull_rom(std::__1::vector<std::__1::array<float, 3ul>, std::__1::allocator<std::__1::array<float, 3ul> > >&&, bool, float) (a.out:x86_64+0x10000dc38)  
    #9 0x10d23f9d1 in boost::math::catmull_rom<std::__1::array<float, 3ul>, std::__1::vector<std::__1::array<float, 3ul>, std::__1::allocator<std::__1::array<float, 3ul> > > >::catmull_rom(std::__1::vector<std::__1::array<float, 3ul>, std::__1::allocator<std::__1::array<float, 3ul> > >&&, bool, float) (a.out:x86_64+0x1000029d1)  
    #10 0x10d23ee09 in main (a.out:x86_64+0x100001e09)  
    #11 0x7fff20414f5c in start (libdyld.dylib:x86_64+0x15f5c)  
```  
  
My bad; will look into it.

---

## Comment 2

> Username: TevansMusco  
> Created at: 2021-05-26 20:14:19 UTC  
> Url: https://github.com/boostorg/math/issues/635#issuecomment-849086987  

Thanks for replying so quickly!

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-05-27 12:53:38 UTC  
> Url: https://github.com/boostorg/math/issues/635#issuecomment-849609506  

Should be fixed in #636 ; code to verify that the start and endpoints are the same:  
  
```  
#include <iostream>  
#include <boost/math/interpolators/catmull_rom.hpp>  
#include <array>  
  
using Vector3f = std::array<float, 3>;  
  
int main() {  
  
    std::vector<Vector3f> pts; // Setup list of points  
    pts.push_back({-5.0f, -5.0f, 0.0f});  
    pts.push_back({5.0f, -5.0f, 0.0f});  
    pts.push_back({5.0f, 5.0f, 0.0f});  
    pts.push_back({-5.0f, 5.0f, 0.0f});  
  
    boost::math::catmull_rom<Vector3f> spline(std::move(pts), true, 1.0); // Setting closed to true triggers the error  
    Vector3f f = spline(spline.max_parameter()); // Index out of bounds for m_s on line 163 of catmull_rom.hpp  
  
    Vector3f g = spline(0.0);  
  
    std::cout << "Begin = {" << g[0] << ", " << g[1] << ", " << g[2] << "}\n";  
    std::cout << "End   = {" << f[0] << ", " << f[1] << ", " << f[2] << "}\n";  
}  
```
