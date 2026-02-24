# #99 - some intrusive containers are not trivially destructible when possible [Closed]

> Username: RFoe  
> Created at: 2025-11-03 05:54:57 UTC  
> Updated at: 2025-12-08 20:31:52 UTC  
> Closed at: 2025-12-08 20:31:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/99  

trivially destruction is prefer because it makes memory reuse/punning simpler and binary smaller and so on  
  
for example  
```cpp  
   ~slist_impl()  
   {  
      BOOST_IF_CONSTEXPR(is_safe_autounlink<ValueTraits::link_mode>::value){  
         this->clear();  
         node_algorithms::init(this->get_root_node());  
      }  
   }  
````  
  
consider this:  
  
```  
  
#if defined(__cpp_concepts) && __cplusplus >= 202002L   
   ~slist_impl() requires is_safe_autounlink<ValueTraits::link_mode>::value   
   {  
         this->clear();  
         node_algorithms::init(this->get_root_node());  
   }  
   constexpr ~slist_impl() noexcept = default;  
#elif  
   ~slist_impl()  
   {  
      BOOST_IF_CONSTEXPR(is_safe_autounlink<ValueTraits::link_mode>::value){  
         this->clear();  
         node_algorithms::init(this->get_root_node());  
      }  
   }  
#endif     
  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-12-08 00:22:34 UTC  
> Url: https://github.com/boostorg/intrusive/issues/99#issuecomment-3623907698  

Thanks for your proposal. The conditionally defaulted destructor will been added to both containers and hooks. Hooks will also be conditionally trivially constructible.

---

## Comment 2

> Username: RFoe  
> Created at: 2025-12-08 10:26:21 UTC  
> Url: https://github.com/boostorg/intrusive/issues/99#issuecomment-3626160165  

Glad to here that
