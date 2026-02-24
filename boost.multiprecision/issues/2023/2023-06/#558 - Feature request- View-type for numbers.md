# #558 - Feature request: View-type for numbers [Open]

> Username: liss-h  
> Created at: 2023-06-01 08:04:39 UTC  
> Updated at: 2023-06-02 07:16:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/558  

Hi there,  
is there any plan to have a "view" type for numbers? (I.e. something like what `std::string_view` is to `std::string`)?  
  
The usecase we had in mind would be a type "views" into the internals of a boost multiprecision number stored in some user defined backend storage, and is primarily used for the purpose of abstracting away the allocator on the backend-frontend interface boundary (like `std::basic_string_view<char, char_traits>` can do for `std::basic_string<char, char_traits, alloc>`). I know, that is potentially quite a niche usecase.  
  
So, basically 3 questions:  
1. Like I asked on top: Is there already a plan to implement something like this?  
2. If there isn't any plan: Would you accept a contribution?  
3. If you do accept contributions: Do you foresee any major blockers that would prevent the implementation of that feature?

---

## Comment 1

> Username: mborland  
> Created at: 2023-06-02 07:16:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/558#issuecomment-1573270001  

>   
> 1. Like I asked on top: Is there already a plan to implement something like this?  
  
I am not aware of any plan for this feature.  
  
> 2. If there isn't any plan: Would you accept a contribution?  
  
Contributions are always welcome.  
  
> 3. If you do accept contributions: Do you foresee any major blockers that would prevent the implementation of that feature?  
  
The language standard is C++14, and if you use any boost features (e.g. Boost string_view) make sure that they can be contextually disabled with `BOOST_MP_STANDALONE` to keep the standalone mode functioning.
