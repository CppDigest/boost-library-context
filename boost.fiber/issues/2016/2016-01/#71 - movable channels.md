# #71 - movable channels? [Closed]

> Username: dvd0101  
> Created at: 2016-01-04 12:11:20 UTC  
> Updated at: 2016-11-18 07:43:36 UTC  
> Closed at: 2016-11-18 07:43:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/71  

Both bound and unbound channels are not copyable nor movable, and thus it cannot be used with std container.  
  
It's possible to make a channel movable or I have to resort to a vector of [shared|unique] pointers?

---

## Comment 1

> Username: olk  
> Created at: 2016-01-04 12:16:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/71#issuecomment-168663728  

(un)bounded_channel uses condition_variable which is not CopyConstructible, MoveConstructible, CopyAssignable, MoveAssignable (as their counterpart in std-namespace).  
at the moment you have to use shared/unique pointer.
