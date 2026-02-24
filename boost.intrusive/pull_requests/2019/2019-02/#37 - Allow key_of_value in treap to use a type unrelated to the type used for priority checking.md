# #37 Allow key_of_value in treap to use a type unrelated to the type used for priority checking [Closed]

> Username: jm-mikkelsen  
> Created at: 2019-02-06 04:32:06 UTC  
> Updated at: 2019-02-13 07:02:32 UTC  
> Closed at: 2019-02-13 07:02:32 UTC  
> Url: https://github.com/boostorg/intrusive/pull/37  

This is to fix issue #35   
  
In commit   
https://github.com/boostorg/intrusive/commit/4cf38987a88e1bb1157f1b8957bc0fc167b576e4 the value used in priority comparison is the result of applying key_of_value. This doesn't seem to be intentional, and stops common cases which were previously allowed from working.  
  
This change goes back to using the value type for priority comparison.

---
