# #150 - Provide base() member for adapted views or locators (Trac 2229) [Open]

> Username: mloskot  
> Created at: 2018-10-08 09:44:49 UTC  
> Updated at: 2019-11-07 10:00:54 UTC  
> Url: https://github.com/boostorg/gil/issues/150  

Moved from https://svn.boost.org/trac10/ticket/2229 description:  
  
> I would like the result of `typename View::template add_deref<fn>::type` to include a member function `View& base()`, much like the iterator adapters currently do (and `std::reverse_iterator`).  
> Imagine I have an rgb-to-hsv converted view, and then want to convert it back to rgb. All I should have to do is call `base()`.  
>  
> The actual use case: I have a deref function that exposes a reference to the element in a gray pixel.  
> I also have a function that takes a view to regular types and creates an adapted view to gray pixels with that element type.  
>  
> I want to be ample to write them so that: `same_types(make_pixel(remove_pixel(T)), T)`

---

## Comment 1

> Username: mloskot  
> Created at: 2018-10-08 09:55:10 UTC  
> Url: https://github.com/boostorg/gil/issues/150#issuecomment-427778321  

I'm not sure how the OP imagined this suggestion in detail, but for `View& base()` we definitely need some extra info cached:  
  
```  
template <typename Loc>  
class image_view  
{  
    using base_type = typename Loc::template add_deref<Deref>::type;  
    {image_view<Loc> or base_type} & base_; //  ???   
  
    image_view() : base_(*this) {}  
    template <typename View>    
    image_view(const View& v) : base_(v) {}  
  
    base_type& base() { return base_; }  
};  
```  
  
Questions (/cc @stefanseefeld & @chhenning):  
  
1. Shall we discuss this idea and try implementing?  
2. Shall we drop the idea for now and come back in future if more users request it?
