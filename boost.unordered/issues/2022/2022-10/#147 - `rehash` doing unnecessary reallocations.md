# #147 - [develop] `rehash` doing unnecessary reallocations [Closed]

> Username: joaquintides  
> Created at: 2022-10-16 09:44:42 UTC  
> Updated at: 2022-10-28 20:17:45 UTC  
> Closed at: 2022-10-28 20:17:45 UTC  
> Url: https://github.com/boostorg/unordered/issues/147  

Consider this program:  
```cpp  
#include <boost/unordered_set.hpp>  
#include <iostream>  
  
static bool log_allocate=false;  
  
template<class T> struct allocator  
{  
  using value_type=T;  
  allocator()=default;  
  template<class U> allocator(allocator<U> const &)noexcept{}  
  template<class U> bool operator==(allocator<U> const &)const noexcept{return true;}  
  template<class U> bool operator!=(allocator<U> const&)const noexcept{return false;}  
  T* allocate(std::size_t n)const    
  {  
    if(log_allocate)std::cout<<"allocate: "<<n<<"\n";  
    return std::allocator<T>().allocate(n);  
  }  
  void deallocate(T* p,std::size_t n)const noexcept{std::allocator<T>().deallocate(p,n);}  
};  
  
int main()  
{  
  boost::unordered_set<int,boost::hash<int>,std::equal_to<int>,::allocator<int>> s(100);  
  for(int i=0;i<s.bucket_count()-1;++i)s.insert(i);  
  std::cout<<"bucket count: "<<s.bucket_count()<<"\n";  
  std::cout<<"about to rehash\n";  
  log_allocate=true;  
  s.rehash(0);  
  std::cout<<"bucket count: "<<s.bucket_count()<<"\n";  
 }  
```  
The output against develop is  
```  
bucket count: 193  
about to rehash  
allocate: 194  
allocate: 4  
bucket count: 193  
```  
where it can be seen that, even though `bucket_count` remains the same after rehashing, reallocation has indeed happened. The problem lies in `table::rehash`:  
```cpp  
template <typename Types>  
inline void table<Types>::rehash(std::size_t num_buckets)  
{  
  num_buckets = (std::max)(  
    min_buckets(size_, mlf_), buckets_.bucket_count_for(num_buckets));  
  
  if (num_buckets != this->bucket_count()) {  
    this->rehash_impl(num_buckets);  
  }  
}  
```  
Unnecessary reallocation happens because, in the example, `num_buckets != this->bucket_count()` but `buckets_.bucket_count_for(num_buckets) == this->bucket_count()`. A simple fix is to rewrite as:  
```cpp  
template <typename Types>  
inline void table<Types>::rehash(std::size_t num_buckets)  
{  
  num_buckets = buckets_.bucket_count_for(  
    (std::max)(min_buckets(size_, mlf_), num_buckets));  
  
  if (num_buckets != this->bucket_count()) {  
    this->rehash_impl(num_buckets);  
  }  
}  
```
