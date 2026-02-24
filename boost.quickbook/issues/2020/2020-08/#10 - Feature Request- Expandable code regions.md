# #10 - Feature Request: Expandable code regions [Open]

> Username: Lastique  
> Created at: 2020-08-24 13:37:58 UTC  
> Updated at: 2020-08-24 13:37:58 UTC  
> Url: https://github.com/boostorg/quickbook/issues/10  

I would like to be able to specify expandable code regions in the examples imported using `import`. Having that in the inline code sample would be nice, too, but not my concern right now.  
  
The primary use case is to be able to include `#include`s in the code samples, hidden by default, so that users are able to view them by clicking on some marker. So, for example, by default the code sample would look like this:  
  
```  
+ #include ...  
  
  void foo()  
  {  
    // example code here  
  }  
```  
  
And after clicking on the "+" it would expand to:  
  
```  
- #include <foo>  
  #include <bar>  
  
  void foo()  
  {  
    // example code here  
  }  
```  
  
I realize this would probably require some JavaScript and I have no idea if the BoostBook backend allows this at all, but the feature would be very useful in Boost.Log docs. In Boost.Log, users are [asking](https://github.com/boostorg/log/issues/37) to see the includes, but the amount of includes sometimes come close in size to the code sample itself, so I'd rather not show them by default.  
  
There may be other cases for this feature as well, such as showing evolution of some code across several code samples, where the pieces of code added previously are shown hidden by default, expandable on click, and the new code is displayed. I'm not sure if there are cases when I'd want an expandable region shown by default, but it would probably be a good idea to have that option. I.e. the markup should probably allow a way to specify if the region has to be hidden or expanded by default.
