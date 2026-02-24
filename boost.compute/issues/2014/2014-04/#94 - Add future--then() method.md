# #94 - Add future::then() method [Closed]

> Username: kylelutz  
> Created at: 2014-04-19 17:57:04 UTC  
> Updated at: 2018-03-10 08:13:23 UTC  
> Closed at: 2018-03-10 08:13:23 UTC  
> Url: https://github.com/boostorg/compute/issues/94  

Add a `future<T>::then()` method which takes a generic function callback and invokes it once the future is ready. Should be trivial once `event::set_callback()` supports generic function callbacks (see issue #86).  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-03-10 08:13:23 UTC  
> Url: https://github.com/boostorg/compute/issues/94#issuecomment-372012264  

Done in #765
