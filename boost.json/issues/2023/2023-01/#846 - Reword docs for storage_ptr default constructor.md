# #846 - Reword docs for storage_ptr default constructor [Closed]

> Username: grisumbras  
> Created at: 2023-01-26 20:05:55 UTC  
> Updated at: 2023-04-02 18:44:46 UTC  
> Closed at: 2023-04-02 18:44:46 UTC  
> Url: https://github.com/boostorg/json/issues/846  

It says "default memory resource" which seems to imply `get_default_memory_resource()`, but it's not actually that, which may create confusion.
