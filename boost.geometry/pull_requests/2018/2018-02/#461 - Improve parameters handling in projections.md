# #461 Improve parameters handling in projections. [Closed]

> Username: awulkiew  
> Created at: 2018-02-26 19:44:34 UTC  
> Updated at: 2018-03-18 02:28:55 UTC  
> Closed at: 2018-03-18 02:28:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/461  

This PR makes the following changes:  
  
- When loading proj4 parameters the type of value is determined at compile time as it's a part of the `pj_param_X()` function's name instead of input string, together with parameter name. The code is clearer because the parameter names are not polluted and loading should be faster, though I haven't measured the performance.  
  
- `pvalue<T>` is no longer returned from `pj_param_X()`. A value of required type is returned instead.  
  
- `pj_param_X()` overloads taking 3 arguments and returning bool were added. These overloads checks if the parameter exists and then assigns value to the 3rd argument if possible. This allows to check for presence of parameter and assignment in 1 search of parameter list. In the original code in some cases this is done 2 times, to check if the parameter exists and then to get the parameter  
  
- `pj_mkparam()` overload taking name and value as separate arguments was added to avoid parsing string to extract them in cases when they are passed at compile-time in code (defaults of some projections).  
  
- `i` and `f` members of pvalue<T> are removed. They were only used to hold return value in `pj_param()` and assigned to in `pj_mkparam()` which was unnecessary since they were never used. The string value was converted again by `pj_param()` when needed.  
  
- `used` member of `pvalue<T>` is commented out because it's not used anywhere in Boost.Geometry. AFAIU in the original proj4 it is used only in function `pj_get_def()`, which allows to get PROJ.4 command string compatible with the projection definition.  
  
- Exception is thrown if the boolean parameter value is incorrect. This is consistent with the original proj4.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-03-18 02:28:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/461#issuecomment-373967987  

Closing because I'll implement this on top of changes I made elsewhere.

---
