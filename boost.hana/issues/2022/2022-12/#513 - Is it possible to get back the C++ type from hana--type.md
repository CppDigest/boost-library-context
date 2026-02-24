# #513 - Is it possible to get back the C++ type from hana::type? [Closed]

> Username: Kelvinyu1117  
> Created at: 2022-12-19 13:11:50 UTC  
> Updated at: 2022-12-21 12:41:38 UTC  
> Closed at: 2022-12-21 12:41:38 UTC  
> Url: https://github.com/boostorg/hana/issues/513  

Hi everyone, I'm new to hana and I want to play around with some metaprogramming magic with hana.  
  
Is there any function that allows me to convert `hana::type<T>` back to `T`?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2022-12-19 19:59:38 UTC  
> Url: https://github.com/boostorg/hana/issues/513#issuecomment-1358209797  

The simplest, most common way to access the contained type is via the `type` member: `typename some_type::type`.  
Other techniques may involve type deduction, and there is also the `Metafunction` concept which provides a few functions for applying types to templates in various ways.  
  
Be sure to check out the "Lvalues and Rvalues" section under the documentation for `type`.  
http://boostorg.github.io/hana/structboost_1_1hana_1_1type.html
