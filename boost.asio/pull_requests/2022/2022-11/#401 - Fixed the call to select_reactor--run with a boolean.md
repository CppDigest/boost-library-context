# #401 Fixed the call to select_reactor::run with a boolean [Closed]

> Username: tonakan  
> Created at: 2022-11-09 18:42:35 UTC  
> Updated at: 2022-11-10 13:37:01 UTC  
> Closed at: 2022-11-10 13:37:01 UTC  
> Url: https://github.com/boostorg/asio/pull/401  

After updating the signature of select_reactor::run(bool block, op_queue<operation>& ops) to select_reactor::run(long usec, op_queue<operation>& ops) (https://github.com/boostorg/asio/commit/b60e92b13ef68dfbb9af180d76eae41d22e19356#diff-de75c3dac52db210acae8bbdeb3d9eb437bdb716cc420e0169987986c9ed625cR189),   
it was still called with a boolean.

---

## Comment 1

> Username: tonakan  
> Created_at: 2022-11-09 19:20:28 UTC  
> Url: https://github.com/boostorg/asio/pull/401#issuecomment-1309246762  

@chriskohlhoff please take a look

---
