# #50 - Add member type to `member_descriptor` [Open]

> Username: Lastique  
> Created at: 2025-02-17 19:53:20 UTC  
> Updated at: 2025-02-18 13:42:18 UTC  
> Url: https://github.com/boostorg/describe/issues/50  

I have a use case where I would like to process types of members of a described struct. While it is possible to deduce the member type from `member_descriptor::pointer`, I think it would be helpful if the type was readily available as a `member_descriptor::type` typedef. This would save the user from having to define helper traits such as this:  
  
```  
template< typename T >  
struct member_type;  
  
template< typename T, typename U >  
struct member_type< T (U::*) >  
{  
    using type = T;  
};  
  
template< typename Descr >  
struct deduce_member_type :  
    member_type< std::remove_cv_t< decltype(Descr::pointer) > >  
{  
};  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2025-02-18 13:42:16 UTC  
> Url: https://github.com/boostorg/describe/issues/50#issuecomment-2665755924  

Also, if you'd like to indicate the class which the member belongs to (in case of inherited members), you could add a typedef for it as well. But this is not needed for my use case.
