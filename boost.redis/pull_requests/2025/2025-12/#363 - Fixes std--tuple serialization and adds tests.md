# #363 Fixes std::tuple serialization and adds tests [Merged]

> Username: anarthal  
> Created at: 2025-12-01 11:41:47 UTC  
> Updated at: 2025-12-02 10:20:04 UTC  
> Merged at: 2025-12-02 10:20:01 UTC  
> Closed at: 2025-12-02 10:20:01 UTC  
> Url: https://github.com/boostorg/redis/pull/363  

* Fixes a problem that caused passing ranges containing tuples into `request::push_range` to generate invalid commands.  
* Adds test_serialization  
* Updates request reference docs to reflect the requirements of the types passed to push and push_range  
  
close #360

---
