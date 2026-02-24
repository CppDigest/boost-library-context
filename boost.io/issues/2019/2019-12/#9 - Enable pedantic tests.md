# #9 - Enable pedantic tests [Closed]

> Username: Flamefire  
> Created at: 2019-12-17 17:26:09 UTC  
> Updated at: 2019-12-24 04:05:09 UTC  
> Closed at: 2019-12-24 04:05:09 UTC  
> Url: https://github.com/boostorg/io/issues/9  

As this library is pretty much self-contained I'd recommend to enable pedantic warnings and Werror by adding   
  
```  
project : requirements  
  <warnings>pedantic  
  <warnings-as-errors>on ;  
```  
  
to the top of the test/Jamefile.v2. This allows downstream projects to build with the same warning level and avoids surprises later when a user is using a higher warning level. Additionally it gives some protection against bugs the compiler can catch easily.  
  
Example problem for downstream: https://github.com/boostorg/io/commit/e276f63390a225df3af442889258078f9be27cdb#diff-a03a32e4bb8dfbae73e4d575219a7f0eR15 has a trailing semicolon   
  
Thanks a lot!

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-24 04:05:09 UTC  
> Url: https://github.com/boostorg/io/issues/9#issuecomment-568645690  

Done in aa70414fde0c3b337dcaa849b61241efd581a5f4.
