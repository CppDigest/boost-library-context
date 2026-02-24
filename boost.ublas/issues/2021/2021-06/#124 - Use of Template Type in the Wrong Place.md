# #124 - Use of Template Type in the Wrong Place [Open]

> Username: amitsingh19975  
> Created at: 2021-06-04 16:49:43 UTC  
> Updated at: 2022-02-10 15:23:04 UTC  
> Url: https://github.com/boostorg/ublas/issues/124  

The template type, which is used for the type of extents harder coded to `std::size_t` instead of using the template type provided by the user. The user-provided type is misused to define the size of the static rank extents.  
  
[Link](https://github.com/boostorg/ublas/blob/db29cdf76cdd4c3732164c99e9a22e9e02bf994f/include/boost/numeric/ublas/tensor/extents/extents_static_size.hpp#L41) to the bug.  
  
Fix:  
  
```cpp  
template <integral T, T N>  
class extents_core<T,N> {  
public:    
       using base_type       = std::array<T,N>;  
....  
}  
```

---

## Comment 1

> Username: shivamt2  
> Created at: 2021-06-28 13:38:13 UTC  
> Url: https://github.com/boostorg/ublas/issues/124#issuecomment-869693053  

Hi, I would like to work on this.

---

## Comment 2

> Username: amitsingh19975  
> Created at: 2021-06-28 13:39:16 UTC  
> Url: https://github.com/boostorg/ublas/issues/124#issuecomment-869693923  

Go ahead.

---

## Comment 3

> Username: Neel-Shah-29  
> Created at: 2022-02-10 14:00:43 UTC  
> Url: https://github.com/boostorg/ublas/issues/124#issuecomment-1034955976  

Can I try to solve this issue? Also guide me once through the issue that we need to replace std::size_t to the template type provided by the user in all the files right?

---

## Comment 4

> Username: amitsingh19975  
> Created at: 2022-02-10 15:23:04 UTC  
> Url: https://github.com/boostorg/ublas/issues/124#issuecomment-1035047864  

> Can I try to solve this issue? Also guide me once through the issue that we need to replace std::size_t to the template type provided by the user in all the files right?  
  
It will be fixed with the bug fix for the extents. So, there's no need for it.
