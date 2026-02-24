# #16 get_option_name() can throw std::logic_error [Closed]

> Username: ya1gaurav  
> Created at: 2015-10-06 03:49:44 UTC  
> Updated at: 2015-10-06 06:46:28 UTC  
> Closed at: 2015-10-06 06:46:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/16  

get_option_name()  calls get_canonical_option_prefix()  which throws in file value_semantic.cpp line no 296  
  
296             throw std::logic_error("error_with_option_name::m_option_style can only be "   
297                               "one of [0, allow_dash_for_short, allow_slash_for_short, "   
298                               "allow_long_disguise or allow_long]");   
299    }

---

## Comment 1

> Username: vprus  
> Created_at: 2015-10-06 06:46:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/16#issuecomment-145756063  

Thank you for the patch! It has been cherry-picked to develop.

---
