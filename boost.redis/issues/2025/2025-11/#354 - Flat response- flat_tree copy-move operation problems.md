# #354 - Flat response: flat_tree copy/move operation problems [Closed]

> Username: anarthal  
> Created at: 2025-11-19 16:12:13 UTC  
> Updated at: 2025-11-29 20:35:55 UTC  
> Closed at: 2025-11-29 20:35:55 UTC  
> Url: https://github.com/boostorg/redis/issues/354  

* operator= has an unusual signature. There should be `operator=(flat_tree&&)` and `operator=(const flat_tree&)`.  
* Move operations result in dangling pointers if the data size is inferior to the string SBO. The string is copied but the views are not updated.  
* I'm pretty sure that copy-constructing a default-constructed `flat_tree` triggers this assert: `BOOST_ASSERT_MSG(pos_ < view_tree_.size(), "notify_done called but no nodes added.");`.  
* We need much more tests.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-11-19 16:33:36 UTC  
> Url: https://github.com/boostorg/redis/issues/354#issuecomment-3553645579  

I'm hitting the copy bug in the Sentinel PR, so I'll defer using the flat types until this is solved.
