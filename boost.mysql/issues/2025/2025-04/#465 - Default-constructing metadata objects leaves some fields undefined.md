# #465 - Default-constructing metadata objects leaves some fields undefined [Closed]

> Username: anarthal  
> Created at: 2025-04-02 09:54:24 UTC  
> Updated at: 2025-04-05 17:46:45 UTC  
> Closed at: 2025-04-05 17:46:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/465  

Shouldn't be a concern, as neither users nor the library should be using default-constructed metadata objects. But it shouldn't happen, as a best practice.
