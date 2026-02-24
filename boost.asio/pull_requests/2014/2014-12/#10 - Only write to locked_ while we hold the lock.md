# #10 Only write to locked_ while we hold the lock [Closed]

> Username: tempoz  
> Created at: 2014-12-08 20:30:59 UTC  
> Updated at: 2017-12-02 08:06:00 UTC  
> Closed at: 2017-12-02 08:06:00 UTC  
> Url: https://github.com/boostorg/asio/pull/10  

To avoid race conditions, locked_ should be set to false before we release the lock.

---

## Comment 1

> Username: tempoz  
> Created_at: 2014-12-08 20:49:09 UTC  
> Updated_at: 2014-12-08 20:49:40 UTC  
> Url: https://github.com/boostorg/asio/pull/10#issuecomment-66184493  

This defect was uncovered by Coverity, and this pull request is in reference to Coverity issue CID10236.

---

## Comment 2

> Username: Chaosvex  
> Created_at: 2015-10-22 18:31:00 UTC  
> Url: https://github.com/boostorg/asio/pull/10#issuecomment-150315093  

The scoped lock isn't shared between threads, so this is actually a false positive.

---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2017-12-02 08:06:00 UTC  
> Url: https://github.com/boostorg/asio/pull/10#issuecomment-348676281  

Not applicable, this is a spurious warning from coverity.

---
