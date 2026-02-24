# #179 - Boost unordered maps (unordered and unordered_flat) vs not copyable not moveable types [Closed]

> Username: krawq  
> Created at: 2023-01-07 19:42:41 UTC  
> Updated at: 2023-02-28 22:34:38 UTC  
> Closed at: 2023-02-28 22:34:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/179  

[Stack overflow linked question](https://stackoverflow.com/questions/75043207/boost-unordered-flat-map-and-not-copyable-not-movable-types)  
  
Following code compile (MSVC C++ latest) using std::unordered_map but not with the new boost:unordered_flat_map:  
  
```  
#include "boost/unordered/unordered_flat_map.hpp"  
#include <unordered_map>  
    class Foo  
    {  
    public:  
        Foo() = default;  
        explicit Foo(int x) : m_x_(x) {};  
    private:  
        int m_x_;  
        std::mutex mtx;  
          
    };  
      
    int main(int argc, char** argv)  
    {  
        boost::unordered_flat_map<int,Foo> map_test; //compile with std::unordered_map  
        map_test.try_emplace(1,1);  
        return 0;  
    }  
```  
I dont expect it to work with flat_map as with std::map , guessing as the map need reordering, elements need to be able to move/copy. But I dont get why its working with std::unordered_map and not boost:unordered_flat_map.

---

## Comment 1

> Username: krawq  
> Created at: 2023-01-08 20:00:47 UTC  
> Url: https://github.com/boostorg/unordered/issues/179#issuecomment-1374915089  

Answered on stackoverflow. Closing here

---

## Comment 2

> Username: cmazakas  
> Created at: 2023-01-10 18:02:19 UTC  
> Url: https://github.com/boostorg/unordered/issues/179#issuecomment-1377648498  

Sorry for the late reply!  
  
> But I dont get why its working with std::unordered_map and not boost:unordered_flat_map.  
  
The STL containers store every element in their own allocation. When the containers rehash and the elements are rearranged into new buckets, only the pointers to those elements are moved around so there's no requirement on the user type.  
  
The new unordered flat map stores all of its elements inline with the bucket allocation. This means that when we want to grow the hash table into something larger, we have to either move or copy the elements from the existing allocation to the new one.  
  
We're currently writing some node-based containers where instead of storing the elements inline with the allocation, we only store pointers to the allocated elements so the code will then behave exactly like it would with `std::unordered_map`.  
  
I'll try to ping you when they're considered ready. We should be able to make Boost 1.82 but they'll be ready before that on our `develop` branch.

---

## Comment 3

> Username: krawq  
> Created at: 2023-01-10 19:06:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/179#issuecomment-1377719828  

> Sorry for the late reply!  
>   
> > But I dont get why its working with std::unordered_map and not boost:unordered_flat_map.  
>   
> The STL containers store every element in their own allocation. When the containers rehash and the elements are rearranged into new buckets, only the pointers to those elements are moved around so there's no requirement on the user type.  
>   
> The new unordered flat map stores all of its elements inline with the bucket allocation. This means that when we want to grow the hash table into something larger, we have to either move or copy the elements from the existing allocation to the new one.  
>   
> We're currently writing some node-based containers where instead of storing the elements inline with the allocation, we only store pointers to the allocated elements so the code will then behave exactly like it would with `std::unordered_map`.  
>   
> I'll try to ping you when they're considered ready. We should be able to make Boost 1.82 but they'll be ready before that on our `develop` branch.  
  
But still elements would be stored in contiguous memory buckets ?

---

## Comment 4

> Username: cmazakas  
> Created at: 2023-01-11 17:20:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/179#issuecomment-1379219510  

> But still elements would be stored in contiguous memory buckets ?  
  
For node-based containers, no. The entire goal would be that elements would be in their own heap allocation which removes the requirements for copyable/movable on user types.  
  
This means that buckets would only store a pointer to an element instead of storing the element inline like the flat containers do currently.

---

## Comment 5

> Username: cmazakas  
> Created at: 2023-02-28 22:34:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/179#issuecomment-1449031351  

Node maps are coming in 1.82. Going to mark this as closed.
