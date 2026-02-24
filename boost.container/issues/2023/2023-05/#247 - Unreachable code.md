# #247 - Unreachable code [Closed]

> Username: JVApen  
> Created at: 2023-05-22 12:52:21 UTC  
> Updated at: 2024-09-30 22:20:09 UTC  
> Closed at: 2024-09-30 22:20:09 UTC  
> Url: https://github.com/boostorg/container/issues/247  

I'm logging this bug because we already have several occurrences of this on our codebase.  
Unfortunately, I still haven't been able to figure out what is causing it and still don't have CReduce working on Windows.  
  
Found in boost version 1.77  
  
> boost\container\vector.hpp(138) : error C2220: the following warning is treated as an error  
> boost\container\vector.hpp(138) : warning C4702: unreachable code  
> boost\container\vector.hpp(1820) : warning C4702: unreachable code  
  
This warning does not occur without optimization flags, it does with /O2. We are using the visual studio compiler on version 14.28  
  
  
The related code:  
````  
136|    //Constructors  
137|   BOOST_CONTAINER_FORCEINLINE vec_iterator() BOOST_NOEXCEPT_OR_NOTHROW  
138|      : m_ptr()   //Value initialization to achieve "null iterators" (N3644)  
139|   {}  
````  
  
````  
1808|    template<class ...Args>  
1809|   BOOST_CONTAINER_FORCEINLINE reference emplace_back(BOOST_FWD_REF(Args)...args)  
1810|   {  
1811|      T* const p = this->priv_raw_end();  
1812|      if (BOOST_LIKELY(this->room_enough())){  
1813|         //There is more memory, just construct a new object at the end  
1814|         allocator_traits_type::construct(this->m_holder.alloc(), p, ::boost::forward<Args>(args)...);  
1815|         ++this->m_holder.m_size;  
1816|         return *p;  
1817|      }  
1818|      else{  
1819|         typedef dtl::insert_emplace_proxy<allocator_type, T*, Args...> proxy_t;  
1820|         return *this->priv_insert_forward_range_no_capacity  
1821|            (p, 1, proxy_t(::boost::forward<Args>(args)...), alloc_version());  
1822|      }  
1823|   }  
````  
  
We don't use vector directly, it's either via flat_map/flat_set/small_vector/static_vector/...

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-30 22:20:09 UTC  
> Url: https://github.com/boostorg/container/issues/247#issuecomment-2384281578  

They seem false positives, as they don't appear to happen in other compilers. I've disabled that warning for Boost.Container in the following commit:  
  
https://github.com/boostorg/container/commit/c4cfad7402dca8403a4c82dd1edbc49e865da989
