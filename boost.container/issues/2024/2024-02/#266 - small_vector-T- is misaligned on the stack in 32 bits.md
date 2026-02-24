# #266 - small_vector<T> is misaligned on the stack in 32 bits [Closed]

> Username: troopy28  
> Created at: 2024-02-13 13:33:10 UTC  
> Updated at: 2024-09-30 21:02:38 UTC  
> Closed at: 2024-09-30 21:02:38 UTC  
> Url: https://github.com/boostorg/container/issues/266  

## Description  
  
I recently had a problem using `small_vector` in combination with `boost::geometry`.  
I am using the `boost::geometry::expand` function, by giving it two boxes as arguments (full code at the end). This call is resolved to `envelope_range_of_boxes::apply` in the file `\boost\geometry\algorithms\detail\envelope\range_of_boxes.hpp`. This function contains the following snippet (line ~260) :  
  
```c++  
...  
typedef longitude_interval<coordinate_type> interval_type;  
typedef std::vector<interval_type> interval_range_type;  
...  
interval_range_type intervals;  
...  
```  
  
`interval_range_type` is used as the collection type for a series of geometric calculations. It was revealed upon profiling that these calculations are slow because of a high number of allocations performed by the vector.  
  
Since the number of elements is small (I did not find a case with more than 2 elements), a simple way to avoid allocations is to use a `small_vector` instead of the `std::vector`, which I am not expecting to ever need to go to dynamic storage (I am using `small_vector` and not `static_vector` because I am not 100% certain of this) :   
```c++  
...  
typedef longitude_interval<coordinate_type> interval_type;  
typedef boost::container::small_vector<interval_type, 4> interval_range_type;  
...  
interval_range_type intervals; // <- Not properly aligned :(  
...  
```  
  
But then, when calling `boost::geometry::expand`, an assert is triggered when constructing the `small_vector`, in the `small_vector_allocator<T, VoidAlloc, Options>::internal_storage()` method - `small_vector.hpp` line 545 :  
```  
BOOST_ASSERT((std::size_t(this) % dtl::alignment_of< small_vector_storage_offset<T, allocator_type, Options> >::value) == 0);  
```  
  
It seems like the local variable `intervals` (the `small_vector`) is not being aligned properly on the stack.  
**This only happens in 32 bits.** In 64 bits, it works fine. Please note that I have only been able to test this on **MSVC**, I do not know if it happens on Linux.  
  
## How to reproduce  
  
### 1. boost::geometry modification  
Modify the file `\boost\geometry\algorithms\detail\envelope\range_of_boxes.hpp` to change the container from `std::vector<interval_type>` to `boost::container::small_vector<interval_type, 4>` at line 248 :  
```c++  
typedef boost::container::small_vector<interval_type, 4> interval_range_type;  
```  
  
### 2. Test program  
Test program : the `expand` function simply needs to be called on two geographic boxes (rectangles).  
  
```c++  
// main.cpp  
  
#include <boost/geometry/geometries/box.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/algorithms/expand.hpp>  
#include <boost/geometry/algorithms/make.hpp>  
  
int main()  
{  
    namespace bg = boost::geometry;  
  
    using point_t = bg::model::point<double, 2, bg::cs::geographic<bg::degree>>;  
    using box_type = bg::model::box<point_t>;  
  
    constexpr box_type box_a{ {0.0, 0.0}, {1.0, 1.0} };  
    constexpr box_type box_b{ {3.0, 3.0}, {5.0, 5.0} };  
    box_type accumulated_box = bg::make_inverse<box_type>();  
    bg::expand(accumulated_box, box_a); // <- Triggers the assert.  
    bg::expand(accumulated_box, box_b); // <- Triggers the assert.  
  
    return 0;  
}  
```  
  
  
## Workaround  
  
Creating a structure that inherits from the `small_vector` we want to create, and specifying its alignment using an attribute, seems to result in a properly aligned variable :  
  
```c++  
// Non portable attribute, but it's the idea.  
struct __declspec(align(16)) aligned_small_vector   
	: boost::container::small_vector<interval_type, 4>   
{};  
typedef aligned_small_vector interval_range_type;  
...  
interval_range_type intervals; // <- Properly aligned !  
```

---

## Comment 1

> Username: edouarda  
> Created at: 2024-03-02 12:24:40 UTC  
> Updated at: 2024-03-02 12:47:42 UTC  
> Url: https://github.com/boostorg/container/issues/266#issuecomment-1974783414  

I confirm the existence of this problem on Windows x86 and that the workaround works.  
  
I made the workaround generic:  
  
```cpp  
template <class T, std::size_t N, class Allocator = void, class Options = void>  
struct alignas(16) aligned_small_vector : boost::container::small_vector<T, N, Allocator, Options>  
{  
    using boost::container::small_vector<T, N, Allocator, Options>::small_vector;  
};  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-04-16 12:48:23 UTC  
> Url: https://github.com/boostorg/container/issues/266#issuecomment-2059012975  

It seems that the implementation for aligned_storage from Boost.Move uses double as the aligner type. Unfortunately, there is a compiler bug, If a user declares a variable with 8 byte alignment, such as a double in x86 the compiler will not realign the stack.  
  
Changing the implementation in Boost.Move to use "__declspec(align)" for 8 byte aligned types shows that MSVC will realign the stack properly:  
  
https://github.com/boostorg/move/commit/d0631eec745828207cf251232cef0d681cb758d8  
  
Could you test that after aplying Boost.Move's commit the issues is fixed?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-09-30 21:02:38 UTC  
> Url: https://github.com/boostorg/container/issues/266#issuecomment-2384139557  

Closing this as fixed.
