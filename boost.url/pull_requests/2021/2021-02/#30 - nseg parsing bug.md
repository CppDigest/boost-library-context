# #30 nseg parsing bug [Closed]

> Username: zajo  
> Created at: 2021-02-21 01:37:47 UTC  
> Updated at: 2021-03-05 20:27:57 UTC  
> Closed at: 2021-03-05 20:27:57 UTC  
> Url: https://github.com/boostorg/url/pull/30  

Added tests for `.segments().size()`. This test was failing:  
  
```  
BOOST_TEST(url_view("/").segments().size() == 1);  
```  
  
The fix is to move `++pt.nseg` (which was on line 662 in `parse.hpp`) ahead of the `if`. Please review the surrounding code to confirm that this is correct.  
  
(My editor deletes any existing trailing white space, which I assume you don't want in the source).

---
