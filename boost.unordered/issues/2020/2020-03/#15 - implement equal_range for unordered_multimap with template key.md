# #15 - implement equal_range for unordered_multimap with template key [Closed]

> Username: amirhakh  
> Created at: 2020-03-30 08:35:48 UTC  
> Updated at: 2021-12-22 23:24:28 UTC  
> Closed at: 2021-12-22 23:24:28 UTC  
> Url: https://github.com/boostorg/unordered/issues/15  

Dear All  
  
Can you implement template<class K> equal_range like std library?  
  
```C++  
template< class K >  
std::pair<iterator,iterator> equal_range( const K& x );  
```  
can read more in:  
https://en.cppreference.com/w/cpp/container/unordered_multimap/equal_range

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-12-22 23:24:28 UTC  
> Url: https://github.com/boostorg/unordered/issues/15#issuecomment-999938097  

Hello @amirhakh , this has been added with #61   
  
I'm going to close this issue now. Feel free to re-open if you have any questions or concerns.
