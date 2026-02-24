# #3 Fix warnings about struct/class mismatch [Merged]

> Username: Lastique  
> Created at: 2014-03-08 15:31:45 UTC  
> Updated at: 2014-07-14 12:17:05 UTC  
> Merged at: 2014-03-08 18:36:20 UTC  
> Closed at: 2014-03-08 18:36:20 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/3  

ms_allocator_state template was declared as class but its specializations were as structs. This caused Clang 3.2 warnings. The commit changes ms_allocator_state declaration to struct to fix that.

---

## Comment 1

> Username: glenfe  
> Created_at: 2014-03-08 18:38:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/3#issuecomment-37105660  

Thanks Andrey; merged.

---
