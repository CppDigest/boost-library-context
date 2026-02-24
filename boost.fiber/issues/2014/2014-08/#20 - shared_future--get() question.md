# #20 - shared_future::get() question [Closed]

> Username: niXman  
> Created at: 2014-08-18 15:17:35 UTC  
> Updated at: 2014-08-19 06:13:15 UTC  
> Closed at: 2014-08-19 06:13:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/20  

Hi,  
  
As I can see here[1], member-function has the signature: `R const& shared_future<R>::get() const`. But here[2], it has the different signature(it returns non-const reference): `R& shared_future<R&>::get() const`.  
Is everything right here?  
  
[1] https://github.com/niXman/boost-fiber/blob/master/include/boost/fiber/future/future.hpp#L614  
[2] https://github.com/niXman/boost-fiber/blob/master/include/boost/fiber/future/future.hpp#L794

---

## Comment 1

> Username: olk  
> Created at: 2014-08-19 06:13:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/20#issuecomment-52594276  

It's correct shared_future< R >::get() has another return type as shared_future< R& >::get() (take a look at C++11).
