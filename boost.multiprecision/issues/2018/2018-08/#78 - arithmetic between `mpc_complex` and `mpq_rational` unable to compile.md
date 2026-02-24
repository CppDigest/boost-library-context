# #78 - arithmetic between `mpc_complex` and `mpq_rational` unable to compile [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-22 20:59:09 UTC  
> Updated at: 2018-10-06 18:32:42 UTC  
> Closed at: 2018-10-06 18:32:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/78  

i currently cannot compile code that asks to multiply a complex by a rational.  the following test fails to compile:  
  
```  
BOOST_AUTO_TEST_CASE(interoperability_with_rational)  
{  
	mpc_complex z;  
	mpq_rational r;  
  
	mpc_complex w = z*r;  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-10-06 18:32:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/78#issuecomment-427596460  

Should now be fixed in develop.
