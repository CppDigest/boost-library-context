# #170 - buffered_channel::try_push has incorrect documentation [Closed]

> Username: ldionne  
> Created at: 2018-03-20 22:45:45 UTC  
> Updated at: 2018-09-25 14:03:55 UTC  
> Closed at: 2018-03-22 07:35:57 UTC  
> Url: https://github.com/boostorg/fiber/issues/170  

The documentation for `buffered_channel::try_push` says:  
  
> If channel is closed, returns closed. Otherwise enqueues the value in the channel, wakes up a fiber blocked on `this->pop()`, `this->value_pop()`, `this->pop_wait_for()` or `this->pop_wait_until()` and returns success.  
  
However, this is the behavior of `buffered_channel::push`. The behavior for `try_push` should be to return `full` without blocking when the channel is full. The implementation of `try_push` seems to agree with me.

---

## Comment 1

> Username: olk  
> Created at: 2018-03-22 07:35:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/170#issuecomment-375205075  

> wakes up a fiber blocked on this->pop(), this->value_pop(), this->pop_wait_for() or this->pop_wait_until()  
  
is correct ... it needs only an addtional description what happens if the channel is full

---

## Comment 2

> Username: olk  
> Created at: 2018-03-22 07:36:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/170#issuecomment-375205212  

ty

---

## Comment 3

> Username: rkfg-bc  
> Created at: 2018-09-25 14:03:37 UTC  
> Updated at: 2018-09-25 14:03:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/170#issuecomment-424356193  

In 1.68 it's changed wrongly, now the `try_push` and `push` descriptions are exactly the same while `push` actually blocks if the buffer is full. The documentation says it doesn't block.
