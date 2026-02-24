# #193 - Wrong behavior of map_array copy constructor. [Open]

> Username: dlee-sandbox  
> Created at: 2024-03-21 22:50:45 UTC  
> Updated at: 2024-03-21 22:50:45 UTC  
> Url: https://github.com/boostorg/ublas/issues/193  

map_array copy constructor is not functions as expected, leading to crash.  
  
Code to reproduce:  
  
```C++  
boost::numeric::ublas::map_array<size_t, float> getMapArray()  
{  
  boost::numeric::ublas::map_array<size_t, float> ma;  
  ma.insert(std::make_pair(1, 1.0f));  
  
  return ma;  
}  
  
  
int main()  
{  
  boost::numeric::ublas::map_array<size_t, float> ma = getMapArray();  
  return 0;  
}  
  
```  
Fix:  
in line 248 of storage_sparse.hpp,  
```C++  
//std::uninitialized_copy (data_, data_ + capacity_, c.data_); // original implementation  
std::uninitialized_copy (c.data_, c.data_ + c.size_, data_);  
```
