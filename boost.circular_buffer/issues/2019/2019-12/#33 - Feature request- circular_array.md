# #33 - Feature request: circular_array [Open]

> Username: NAThompson  
> Created at: 2019-12-12 16:25:48 UTC  
> Updated at: 2019-12-12 16:38:38 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/33  

Having a compile-time size of a circular buffer is often useful:  
  
```  
boost::circular_array<double, 7> v;  
```  
  
Will attempt to provide this in a forthcoming PR if everyone thinks it's a good idea.

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-12 16:27:56 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/33#issuecomment-565080361  

You might want to talk to @breese who has implemented https://github.com/breese/trial.circular

---

## Comment 2

> Username: glenfe  
> Created at: 2019-12-12 16:30:22 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/33#issuecomment-565081429  

See also the e-mail thread on the Boost mailing list: https://lists.boost.org/Archives/boost/2019/09/246925.php

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-12-12 16:38:38 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/33#issuecomment-565084826  

@breese's work is exactly the way I had in mind to do this.
