# #162 - boost::pfr::names_as_array<T> does not work on structure include c array [Open]

> Username: baker-Xie  
> Created at: 2024-02-23 10:15:07 UTC  
> Updated at: 2024-10-17 09:02:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/162  

demo code like this  
```  
#include "boost/pfr.hpp"  
  
struct some_person {  
    float data[3];  
};  
  
int main(int argc, const char* argv[]) {  
    auto name = boost::pfr::names_as_array<some_person>();  
}  
```

---

## Comment 1

> Username: XRay3D  
> Created at: 2024-05-16 08:33:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/162#issuecomment-2114487831  

Incorrectly defines structures with nested arrays. If the arrays are length of 1, then everything works correctly.  
```cpp  
struct {  
    float arr1[3]{};  
    float arr2[3]{};  
} array1;  
float array2[3][2]{};  
  
qWarning() << pfr::tuple_size_v<decltype(array1)>; // 6  
qWarning() << pfr::tuple_size_v<decltype(array2)>; // 6  
```

---

## Comment 2

> Username: p00f  
> Created at: 2024-09-16 14:19:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/162#issuecomment-2353066251  

@baker-Xie @XRay3D did you find a solution or a non-boost way to do this? i want the name + value of struct fields

---

## Comment 3

> Username: XRay3D  
> Created at: 2024-09-28 18:23:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/162#issuecomment-2380856483  

> @baker-Xie @XRay3D did you find a solution or a non-boost way to do this? i want the name + value of struct fields  
  
Try use ```std::array```.

---

## Comment 4

> Username: p00f  
> Created at: 2024-09-28 18:40:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/162#issuecomment-2380860835  

that is not an option - i don't control the structs i'm reflecting on 😅
