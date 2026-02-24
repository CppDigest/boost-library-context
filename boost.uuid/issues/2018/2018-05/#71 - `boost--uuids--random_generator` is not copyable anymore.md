# #71 - `boost::uuids::random_generator` is not copyable anymore. [Closed]

> Username: Klaim  
> Created at: 2018-05-10 15:52:06 UTC  
> Updated at: 2018-06-18 14:24:48 UTC  
> Closed at: 2018-06-18 14:24:48 UTC  
> Url: https://github.com/boostorg/uuid/issues/71  

Before Boost v1.67, `boost::uuids::random_generator`  was set to the boost random mtwiter random engine which is copyable.   
In Boost v1.67 (thankfully and finally) it is not set to that random engine but to a new one based on the platform API. Unfortunately it is now not copyable like the previous implementation.  
  
I'm not sure if required to not be copyable but the change can be problematic.  
Would it be possible to make it moveable and copyable?

---

## Comment 1

> Username: Lastique  
> Created at: 2018-05-10 16:39:05 UTC  
> Url: https://github.com/boostorg/uuid/issues/71#issuecomment-388110274  

Given that `random_generator` can now hold various system handles, it cannot be made copyable without introducing reference counting, which I don't think is a good idea. It can be made movable, though.

---

## Comment 2

> Username: MarcelRaad  
> Created at: 2018-05-10 17:02:02 UTC  
> Url: https://github.com/boostorg/uuid/issues/71#issuecomment-388117041  

Making it movable would help us very much for code that has to work with old (1.65) and new Boost versions. Either having to have two implementations of that code or having to use the old implementation, both of which I'd prefer to avoid, is the reason I haven't updated to 1.67 yet.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-05-10 17:06:03 UTC  
> Url: https://github.com/boostorg/uuid/issues/71#issuecomment-388118250  

To restore the old implementation of `random_generator` you can define `BOOST_UUID_RANDOM_GENERATOR_COMPAT`.

---

## Comment 4

> Username: Klaim  
> Created at: 2018-05-10 17:21:48 UTC  
> Url: https://github.com/boostorg/uuid/issues/71#issuecomment-388122702  

I had already a typedef so it was easy to switch to the mersene twister one, but I would like to use the new one if possible.  
  
Making it move-able would be enough for me.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-06-17 03:57:16 UTC  
> Url: https://github.com/boostorg/uuid/issues/71#issuecomment-397852962  

I've been trying to make it movable over the last couple days but it isn't quite working yet.
