# #27 Basic move semantic for ptree [Open]

> Username: maciekgajewski  
> Created at: 2017-05-14 16:17:22 UTC  
> Updated at: 2024-06-24 09:44:25 UTC  
> Url: https://github.com/boostorg/property_tree/pull/27  

Added move semantic for the container: move constructor and move assignment,  
and also move versions of the following methods:  
- push_back  
- push_front  
- insert  
  
In the future, I'd also like to add move versions of the put/add family.

---

## Comment 1

> Username: nvkhoi112358  
> Created_at: 2024-06-14 10:38:16 UTC  
> Updated_at: 2024-06-24 09:44:25 UTC  
> Url: https://github.com/boostorg/property_tree/pull/27#issuecomment-2167748420  

Any reason that this is not accepted to master?  
Also, I think the `add_child` should support move semantic too.  
For big ptree, it cost a lot on copying.  
  
Current I have to add/put first, then modify on returned reference.

---
