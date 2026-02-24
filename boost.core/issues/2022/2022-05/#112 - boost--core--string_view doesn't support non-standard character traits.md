# #112 - boost::core::string_view doesn't support non-standard character traits [Open]

> Username: sehe  
> Created at: 2022-05-02 05:17:52 UTC  
> Updated at: 2025-12-14 20:14:34 UTC  
> Url: https://github.com/boostorg/core/issues/112  

`boost::core::basic_string_view` replicates the standard `std::string_view` interface. However, it lacks support for the char_trait template argument wholesale.  
  
While this omission is defensible, it might be a reason stopping people from using `core::basic_string_view` in their code.  
  
-----  
  
¹  except for conversion from/to std::basic_string, for obvious reasons

---

## Comment 1

> Username: pdimov  
> Created at: 2025-12-14 20:14:34 UTC  
> Url: https://github.com/boostorg/core/issues/112#issuecomment-3651989644  

I'm not sure I understand the "obvious reasons" :-)
