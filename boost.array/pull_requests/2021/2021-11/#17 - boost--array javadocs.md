# #17 boost::array javadocs [Open]

> Username: alandefreitas  
> Created at: 2021-11-18 16:43:25 UTC  
> Updated at: 2021-11-18 18:51:03 UTC  
> Url: https://github.com/boostorg/array/pull/17  

This PR adds javadocs to `array.hpp`. This is the [output preview with b2 + doxygen](https://alandefreitas.github.io/boost-array-docs/boost/array.html).   
  
Unfortunately, the b2 doxygen rule does not support [member grouping](https://www.doxygen.nl/manual/grouping.html#memgroup), which would nicely split the member functions [like this](https://alandefreitas.github.io/boost-array-docs/doxygen_html/classboost_1_1array.html), which is also closer to [cppreference for std::array](https://en.cppreference.com/w/cpp/container/array).   
  
In fact, the rule *removes* member groups from the output, so these javadocs had to be replaced by simple comments identifying the groups.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2021-11-18 16:54:09 UTC  
> Url: https://github.com/boostorg/array/pull/17#issuecomment-973053805  

@pdimov just warned me the notes are wrong

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2021-11-18 18:38:41 UTC  
> Url: https://github.com/boostorg/array/pull/17#issuecomment-973150009  

> @pdimov just warned me the notes are wrong  
  
Squashed and pushed!  
  
Output preview: https://alandefreitas.github.io/boost-array-docs/boost/array.html  
  
Example: https://github.com/alandefreitas/array/blob/54fe87b664c3b30d809ec406f04227625705c7b2/include/boost/array.hpp#L127

---
