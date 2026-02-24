# #212 - Static interface type mismatch errors should be non-fatal [Open]

> Username: anarthal  
> Created at: 2024-02-02 12:25:39 UTC  
> Updated at: 2024-02-02 12:25:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/212  

Currently, when the static interface finds a type mismatch, no more packets are read, thus making further operations impossible.  
  
Rationale: a static interface type mismatch means that a serious error has occurred. Either the type definitions must be updated (which needs rebuilding) or the database definitions are out of sync (can be solved by closing and re-opening the connection, as in a network failure).  
  
However, this causes surprises. Following the principle of least surprise, it would be good to make these errors non-fatal.  
  
See #211
