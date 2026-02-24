# #288 - Pipeline: remove the static interface [Closed]

> Username: anarthal  
> Created at: 2024-06-23 16:57:20 UTC  
> Updated at: 2024-06-26 14:47:28 UTC  
> Closed at: 2024-06-26 14:47:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/288  

After implementing the basics of #285, I strongly believe that the static interface for the pipeline feature creates more problems than it solves. It was originally coded because I was expecting pipeline to be an easy-to-use feature as in Redis, but semantics are different (the `COMMIT` pitfall), so this is no longer the case.  
  
This hasn't been released yet, and should be done before 1.86 closes.
