# #242 - handler_ptr issues [Closed]

> Username: harrishancock  
> Created at: 2017-01-29 22:48:54 UTC  
> Updated at: 2017-02-02 21:07:37 UTC  
> Closed at: 2017-02-01 23:46:33 UTC  
> Url: https://github.com/boostorg/beast/issues/242  

Hey Vinnie, I've been using `handler_ptr` in my own code and ran into three little issues:  
  
1. There is no way to check whether or not a `handler_ptr` is valid. `ptr.get() != nullptr` cannot be used, because that dereferences `p_`, which may be null.  
  
   An `explicit operator bool() const { return p_ != nullptr && p_->t != nullptr; }` would suffice, but it also occurred to me that perhaps `get()` should `return p_ != nullptr ? p_->t : nullptr`? That would prevent calling `ptr.get()` on a moved-from `ptr` from crashing.  
  
   My use case is that I'd like to use assertions to check for programming errors in my code.  
  
2. Much less important, but an `element_type = T` member typedef would be nice. I reached for one while specializing a traits class and missed it from the standard smart pointers.  
  
3. `make_handler_ptr` seems to have missed the documentation. Perhaps Doxygen ignores friend function declarations, and needs them declared outside the class as well?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-30 00:29:21 UTC  
> Url: https://github.com/boostorg/beast/issues/242#issuecomment-275958941  

Great suggestions, I agree with everything. Not sure what happened with `make_handler_ptr`. One thing I should point out, the "quick reference" is maintained by hand, and I did forget to put `make_handler_ptr` in there:  
https://github.com/vinniefalco/Beast/blob/master/doc/quickref.xml#L178  
  
However, that doesn't explain why `make_handler_ptr` is not appearing in the automatically generated Index:  
http://vinniefalco.github.io/beast/beast/index.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-01-30 01:05:31 UTC  
> Updated at: 2017-01-30 01:05:52 UTC  
> Url: https://github.com/boostorg/beast/issues/242#issuecomment-275961349  

This will go in to **1.0.0-b25**  
https://github.com/vinniefalco/Beast/commit/d3368d0b9f1f8392f9ee2a74830c210f28720f36

---

## Comment 3

> Username: harrishancock  
> Created at: 2017-01-30 03:30:55 UTC  
> Url: https://github.com/boostorg/beast/issues/242#issuecomment-275973525  

Thanks for the quick turn around! Two things you may or may not have already spotted:  
  
1. gcc 5.4 complains thusly:  
   ```  
   /.../beast/core/handler_ptr.hpp: In function ‘beast::handler_ptr<T, Handler> beast::make_handler_ptr(Handler&&, Args&& ...)’:  
   /.../beast/core/handler_ptr.hpp:189:12: error: ‘make_handler_ptr_helper’ was not declared in this scope  
     return make_handler_ptr_helper<T, Handler>(  
            ^  
   ```  
   and again on line 198.  
  
   I copied and pasted the friend declarations verbatim (minus the `friend` word) outside the class scope, above `make_handler_ptr` definitions, and it all compiled and worked correctly. (Could the two functions be folded into one? I presume some compiler gave you a pragmatic reason to separate them.)  
  
2. `unit_test/dstream.hpp` crept into `websocket/impl/write.ipp`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-01-30 12:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/242#issuecomment-276048767  

Updated: https://github.com/vinniefalco/Beast/commit/a44785abc7cdc6b67afd46c25dc820797d512560

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-02 12:35:33 UTC  
> Url: https://github.com/boostorg/beast/issues/242#issuecomment-276946088  

Note that the API has changed slightly - for the better I hope!

---

## Comment 6

> Username: harrishancock  
> Created at: 2017-02-02 21:07:37 UTC  
> Url: https://github.com/boostorg/beast/issues/242#issuecomment-277084123  

Looks good -- I agree, for the better! Thanks again. :)
