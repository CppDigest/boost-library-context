# #29 - New range based set methods have caused ambiguity [Closed]

> Username: jeking3  
> Created at: 2018-10-21 16:38:56 UTC  
> Updated at: 2018-11-03 20:45:30 UTC  
> Closed at: 2018-11-03 20:45:30 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/29  

Cause:  
#27   
  
See:  
https://github.com/boostorg/dynamic_bitset/pull/27/files#r226869343  
https://github.com/rdkit/rdkit/issues/2128

---

## Comment 1

> Username: Izaron  
> Created at: 2018-10-21 18:20:40 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/29#issuecomment-431691504  

Yes, I was thinking about this issue and wrote this in the closed PR:  
  
> But I have a serious doubt connected with implicit conversions from int to bool.  
> If a programmer paid no attention to warnings and/or had to code really fast, he could write this:  
> ```  
> int okay = 42;  
> /* Transforming 'okay' */  
> db.set(pos, okay);  
> ```  
> The last line was supposed to be `db.set(pos, okay != 0)` according to the current state, but having a new function `dynamic_bitset& set(size_t n, size_t len, bool value = true)` makes it into `db.set(pos, okay, true)` implicitly, which seems to me a serious thing...  
>   
  
And well, it already caused some problems. I consider a possibility to rename this function to smth else. We may have the only function `set` but multiple versions of function, let's say, `change`, that do range operations.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-22 01:18:30 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/29#issuecomment-431721684  

Sounds good to me.  Are you going to issue a PR for that?

---

## Comment 3

> Username: mclow  
> Created at: 2018-10-24 17:12:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/29#issuecomment-432747147  

Please resolve this (or revert this) before the freeze beta (7-Nov on the [development calendar](https://boost.org/development))

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-03 03:19:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/29#issuecomment-435556336  

@mclow got it.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-03 03:27:46 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/29#issuecomment-435556666  

A simpler resolution is to require the "val" to be specified.  It allows us to keep the set() name but for range based set you have to indicate what you are setting it to.  Seems better than having a differently named change() just for range, or set_range().  If we did set_range, we would need reset_range and flip_range, which really isn't necessary.
