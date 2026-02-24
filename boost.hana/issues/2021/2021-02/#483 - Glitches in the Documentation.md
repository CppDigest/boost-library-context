# #483 - Glitches in the Documentation [Open]

> Username: ricejasonf  
> Created at: 2021-02-15 23:13:42 UTC  
> Updated at: 2022-10-21 20:47:34 UTC  
> Url: https://github.com/boostorg/hana/issues/483  

Many of the Concepts associated functions are either missing, misnamed, or just weird.  
  
For example `Monad` does not show `flatten` or `chain` in the submenu.  
  
Also `ap` is named just `A`.  
  
Most amusingly there is a new function called `mathrm` in `MonadPlus`. :rofl:   
http://boostorg.github.io/hana/group__group-_monad_plus.html#ga5ee54dc1195f9e5cf48bfd51ba231ae5  
 (I think this was `remove_if`)  
  
I have not yet looked to see what is causing this.

---

## Comment 1

> Username: ldionne  
> Created at: 2022-10-21 03:42:15 UTC  
> Url: https://github.com/boostorg/hana/issues/483#issuecomment-1286418310  

Doxygen is broken. TBH I'm not sure what the solution would be short of rewriting the documentation using another tool.. that's kind of messed up.

---

## Comment 2

> Username: beojan  
> Created at: 2022-10-21 20:47:34 UTC  
> Url: https://github.com/boostorg/hana/issues/483#issuecomment-1287422490  

[Standardese](https://github.com/standardese/standardese) might be an option. It looks like it can support LaTeX in comments (using MathJax): https://github.com/standardese/standardese/issues/53.
