# #190 - Classifying types [Open]

> Username: eldiener  
> Created at: 2021-06-17 20:35:08 UTC  
> Updated at: 2021-08-02 22:40:56 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/190  

I do not understand where in the implementation there is functionality for classifying types. In the function_types library there are numerous metafunctions for testing whether a given type is a particular function type. I do not see the equivalent functionality in callable_traits. Does it exist ? Is it documented ? Am I misreading the doc somehow ?

---

## Comment 1

> Username: badair  
> Created at: 2021-08-02 22:40:42 UTC  
> Updated at: 2021-08-02 22:40:56 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/190#issuecomment-891380704  

@eldiener See all the features starting with `has_*` and `is_*` [here](https://www.boost.org/doc/libs/develop/libs/callable_traits/doc/html/index.html). Not provided are features that already exist in C++11, listed below:  
* [is_function](https://en.cppreference.com/w/cpp/types/is_function)  
* [is_member_object_pointer](https://en.cppreference.com/w/cpp/types/is_member_object_pointer)  
* [is_member_function_pointer](https://en.cppreference.com/w/cpp/types/is_member_function_pointer)  
* [is_member_pointer](https://en.cppreference.com/w/cpp/types/is_member_pointer)  
  
It would probably be a good idea for the documentation to mention these. Thanks for bringing this to my attention.
