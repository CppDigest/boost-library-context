# #31 Adds forwarding make_optional helpers, resolves #30 [Merged]

> Username: daniel-j-h  
> Created at: 2017-05-14 14:38:44 UTC  
> Updated at: 2020-10-27 21:14:10 UTC  
> Merged at: 2017-05-17 21:12:40 UTC  
> Closed at: 2017-05-17 21:12:40 UTC  
> Url: https://github.com/boostorg/optional/pull/31  

For #30. The varargs overloads probably make no sense with the `bool` argument at the second position?  
  
I don't quite understand why the `test/optional_test_fail_copying_a_moveable_type.cpp` fails to compile [here](https://github.com/boostorg/optional/blob/cb7641dc34bc61cefa44e9bccfebe02889148528/include/boost/optional/optional.hpp#L350) (unrelated to this changeset) - shouldn't construction from a moveable-only type work?  
  
Also how can I provide docs for the overload depending on if rvalue refs are enabled or not? I found the quickbook files in `doc`  I'm just wondering what the best way to document the overloads is.  
  
cc @akrzemi1

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2017-05-15 06:49:42 UTC  
> Url: https://github.com/boostorg/optional/pull/31#issuecomment-301390627  

The code you highlighted triggers uses `val` as an lvalue: therefore it chooses to copy rather than move.  
For the docs and unit tests, you do not have to bother about these. When I get to merge your change (be patient with me), I will update the docs also. My solution in this case is to only document for the case with rvalue references enabled. For the rest I only say: "if rvalues are disabled, the library degrades in a friendly way".

---

## Review 2 [Commented]

> Username: stevenr3  
> Created_at: 2020-10-21 21:26:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/31#pullrequestreview-514198785  

📁 include/boost/optional/optional.hpp

```diff
1294 |+ }
1295 |+ 
1296 |+ #else
```

> Username: stevenr3  
> Created_at: 2020-10-21 21:26:53 UTC  
> Updated_at: 2020-10-21 21:26:54 UTC  
> Url: https://github.com/boostorg/optional/pull/31#discussion_r509713160  

Was it intended to remove the l-value methods with this `#else`?  
I'm getting a compile error trying to make an optional from a variable, e.g. `float x = 1; make_optional<float>(x);`  
(without `-DBOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES`)

> Username: akrzemi1  
> Created_at: 2020-10-27 20:39:58 UTC  
> Url: https://github.com/boostorg/optional/pull/31#discussion_r513015589  

Function `make_optional` is not intended to be used like this, with template arguments specified explicitly. The only purpose of function `make_optional` is to spare you the trouble of specifying the type of optional value. If you want to specify it yourself, you have a simpler version:  
  
```c++  
float x = 1; boost::optional<float>(x);  
```  
Macro `BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES` is not intended to be defined by users. The library defines it when it detects old compilers without rvalue reference support.

> Username: stevenr3  
> Created_at: 2020-10-27 20:59:03 UTC  
> Url: https://github.com/boostorg/optional/pull/31#discussion_r513026607  

Sorry I missed some detail… I was attempting to use `boost::make_optional<T>(bool, T)` to simplify the following:  
```  
float value;  
bool valid = GetValidAndValue(&value);  
  
// return valid ? boost::optional<float>(value) : boost::none;  
return boost::make_optional<float>(valid, value);  
```  
True it is not a huge simplification - but should that be possible?

> Username: akrzemi1  
> Created_at: 2020-10-27 21:06:55 UTC  
> Url: https://github.com/boostorg/optional/pull/31#discussion_r513032244  

Again, same response. If you can afford spelling out the type of optional value use:  
  
```c++  
return boost::optional<float>(valid, value);  
```  
If you want to the type of optional value to be deduced use:  
  
```c++  
return boost::make_optional(valid, value);  
```

> Username: akrzemi1  
> Created_at: 2020-10-27 21:07:34 UTC  
> Url: https://github.com/boostorg/optional/pull/31#discussion_r513032637  

IOW, `make_optional` is only used for type deduction.

> Username: stevenr3  
> Created_at: 2020-10-27 21:14:09 UTC  
> Updated_at: 2020-10-27 21:14:10 UTC  
> Url: https://github.com/boostorg/optional/pull/31#discussion_r513036489  

Ah ok, got it... thanks!


---
