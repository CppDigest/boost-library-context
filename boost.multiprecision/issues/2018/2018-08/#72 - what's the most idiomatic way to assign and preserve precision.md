# #72 - what's the most idiomatic way to assign and preserve precision? [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-16 19:54:56 UTC  
> Updated at: 2018-08-17 10:06:21 UTC  
> Closed at: 2018-08-17 10:06:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/72  

suppose i have `z`, a variable multiprecision complex at precision p, and `w` at precision `q`, such that `p!=q`.  what's the best way to make `z` be `w`, including all digits and precision?  
  
that is, if  
```  
DefaultPrecision(50);  
mpfr_complex z;  
DefaultPrecision(40);  
mpfr_complex w(sqrt(mpfr_complex(2));  
```  
and i want `z` to be, well and truly, `w`, how do i do this in the best possible fashion?  
  
  
the best i have so far is   
```  
z.precision(w.precision());  
z = w;  
```  
which feels ungainly to me.  is it the best i can do?  is there a way to do it in one line?  should there possibly be some `z.clone(w)` sort of functionality?  
  
the same question applies to `mpfr_float`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-17 10:06:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/72#issuecomment-413819984  

Any of:  
  
```  
z.swap(w);  // swaps values and precision.  
```  
Or:  
```  
z = std::move(w);  // move assignment, destroys w  
```  
Or:  
```  
z = std::move(mpfr_float(w));  // moves a copy into z, w is retained.  
```  
Note that the call to std::move is superfluous, since the temporary copy is an rvalue already, but IMO it helps express intent.  
  
Now documented and tested in the commit referenced above.
