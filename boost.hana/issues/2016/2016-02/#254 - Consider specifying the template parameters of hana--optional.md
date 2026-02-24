# #254 - [optional] Consider specifying the template parameters of hana::optional [Closed]

> Username: ldionne  
> Created at: 2016-02-19 20:47:58 UTC  
> Updated at: 2016-03-18 17:26:49 UTC  
> Closed at: 2016-02-22 04:58:09 UTC  
> Url: https://github.com/boostorg/hana/issues/254  

Currently, `hana::optional` documents constructors, but its template parameters are unspecified. This is slightly inconsistent, since there is no (easy) way to call a constructor without knowing the template parameters of the type. Also, there seems to be valid use cases for explicitly specifying the template parameters of `hana::optional`.  
  
Left to do:  
- See what it would mean in terms of loss of flexibility to implement `optional`.  
- See what code and documentation changes must be done to accommodate this.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-02-21 15:26:33 UTC  
> Url: https://github.com/boostorg/hana/issues/254#issuecomment-186843340  

> See what it would mean in terms of loss of flexibility to implement `optional`.  
  
It turns out that the representation of `optional` is already used in a couple of places in the library, so the flexibility to change is already somewhat lost.  
  
> See what code and documentation changes must be done to accommodate this.  
  
Trivial.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2016-03-17 21:35:20 UTC  
> Updated at: 2016-03-17 21:36:23 UTC  
> Url: https://github.com/boostorg/hana/issues/254#issuecomment-198091353  

I know this is closed, but do you think there is a performance impact with using specializations on a variadic template. Have you considered something like `hana::just_t<T>` and `hana::nothing_t`?   
(not sure if anything makes that infeasible)  
  
It would be interesting to benchmark that.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-03-18 17:26:49 UTC  
> Url: https://github.com/boostorg/hana/issues/254#issuecomment-198462468  

I think I remember benchmarking the cost of having many template specializations at some points, maybe two years ago when working on MPL11. I think I remember there was some difference, but you really need a lot of specializations to make it bad. I really don't think changing the type of `hana::just` and `hana::nothing` would make any noticeable difference. That being said, if you're willing to try changing it, then timing the unit tests would be an easy way to see if there's a difference. If you do it, let me know the results.
