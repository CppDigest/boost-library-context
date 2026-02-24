# #263 Fix unchecked lower bound warning in get_default_error_string. [Open]

> Username: PDeets  
> Created at: 2025-11-15 03:56:20 UTC  
> Updated at: 2025-11-15 03:56:20 UTC  
> Url: https://github.com/boostorg/regex/pull/263  

This resolves #248 by fixing a build warning on MSVC that occurs in the `get_default_error_string` function. The warning is due to n being used as an index without its lower bound being checked. To fix this, I'm adding a cast to the bounds comparison so it is done as unsigned. That way negative values will show up as large positive values that exceed `error_unknown`. For consistency with the surrounding code, I used the same syntax to define the unsigned type as is used in the `is_extended` function on line 45.

---
