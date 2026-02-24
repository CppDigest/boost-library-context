# #289 - Use d_-> not auto& d = *d_; [Closed]

> Username: vinniefalco  
> Created at: 2017-03-30 02:53:01 UTC  
> Updated at: 2017-05-08 02:44:26 UTC  
> Closed at: 2017-05-08 02:44:26 UTC  
> Url: https://github.com/boostorg/beast/issues/289  

`auto& d = *d_` in composed operations is a needless complexity whose original intent was to save typing. Instead, it just confuses people who want to study the implementation. Therefore it should be removed. Replace `d.` with `d_->`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-02 20:40:05 UTC  
> Url: https://github.com/boostorg/beast/issues/289#issuecomment-298754349  

Hmmm, this is not so straightforward as I thought. Consider:  
```  
d_->s.get_io_service().post(bind_handler(std::move(*this), ec, 0));  
```  
This expression will crash since we move `*this` and then access `d_->`. Maybe its fine the way it is.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-02 20:46:17 UTC  
> Url: https://github.com/boostorg/beast/issues/289#issuecomment-298756001  

We could rename it, e.g. `auto& self = *d_` or `auto& self = *data_`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-08 02:44:26 UTC  
> Url: https://github.com/boostorg/beast/issues/289#issuecomment-299759952  

This can't work, it was written that way for a reason.
