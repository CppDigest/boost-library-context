# #112 - `string_view::contains` is undocumented [Open]

> Username: Flamefire  
> Created at: 2025-12-25 17:10:18 UTC  
> Updated at: 2025-12-25 17:10:18 UTC  
> Url: https://github.com/boostorg/utility/issues/112  

The [docs](https://www.boost.org/doc/libs/latest/libs/utility/doc/html/utility/utilities/string_view.html) do not mention the `contains` members.  
  
Also `ends_with` and friends are still marked as "boost extensions": https://github.com/boostorg/utility/blob/e675dfc8139061c9debd0847cecf34963ad6539b/include/boost/utility/string_view.hpp#L244  
  
They are part of C++20 `std::string_view` now.
