# #251 Use class for template template parameter [Merged]

> Username: mloskot  
> Created at: 2019-03-04 21:00:50 UTC  
> Updated at: 2019-03-29 20:27:07 UTC  
> Merged at: 2019-03-05 06:54:14 UTC  
> Closed at: 2019-03-05 06:54:15 UTC  
> Url: https://github.com/boostorg/gil/pull/251  

The current definition using `typename` fails when compiling  
with GCC 4.8 and 4.9, because `typename` is allowed since C++17.  
  
### References  
  
* The C++17 code was introduced in #248  
* Failing CircleCI builds: https://circleci.com/gh/boostorg/gil/7123 and https://circleci.com/gh/boostorg/gil/7125  
  
    ```  
    libs/gil/test/pixel/concepts.cpp:24:39: error: expected 'class' before 'Concept'  
    template <template<typename> typename Concept>  
                                        ^  
    ```  
  
* https://en.cppreference.com/w/cpp/language/template_parameters#Template_template_parameter  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
By the way, we may want to consider using `class` instead of `typename` in general. For example, if we wanted to follow the [C++ Specification Style Guidelines](https://github.com/cplusplus/draft/wiki/Specification-Style-Guidelines):  
> template type parameters use class not typename  
  
@stefanseefeld, @chhenning Any preferences?

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-03-04 21:54:15 UTC  
> Url: https://github.com/boostorg/gil/pull/251#issuecomment-469436944  

In general I prefer "typename" for type parameters (since not all types are classes). However, for template template parameters I do prefer "class", for the same reason: Only classes can be parametrized, not arbitrary types.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-03-04 22:29:39 UTC  
> Url: https://github.com/boostorg/gil/pull/251#issuecomment-469448150  

@stefanseefeld I agree. To me, the `typename` is a misnomer in the non-template-template cases, so I try to avoid it there too.

---
