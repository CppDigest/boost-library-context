# #108 - Conversion to interval [Open]

> Username: mglisse  
> Created at: 2019-01-22 16:50:52 UTC  
> Updated at: 2019-01-22 17:05:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/108  

Hello,  
in our application, we regularly convert a bignum `x` to an interval of double `(i,s)`, similar to what Boost.Interval provides, such that `i<=x<=s`. With Boost.Multiprecision, to convert to an interval, I can first convert to a double `d`, cross my fingers that the conversion was within 1 ulp (should be true for most types), then return the interval `(nextafter(d,-inf), nextafter(d,+inf))`. However, in many places, we have a fast path for the case where the interval is a point. This means that, if the bignum happens to fit exactly in a `double` (unlikely in general, but quite common in some applications), it is important for us to get a tight interval `(d, d)`.  
For `mpz_int` or `mpq_rational`, a way to do that is to access `x.backend().data()` and use MPFR for the conversion.  
For `cpp_int` or `cpp_rational`, I don't know how to get a tight interval. Well, I can actually think of some ways (for instance convert `cpp_int` to double and back to `cpp_int` and compare to the original, etc), but they are going to be ugly and slow compared to something using the internals of `cpp_int`. It would be ideal if Boost.Multiprecision provided, at least for `cpp_int`, a tight conversion to `boost::numeric::interval<double>`, or a conversion to `double` that also lets the user know if this was rounded up, down, or exact (so I know which bound of the interval requires `nextafter`).

---

## Comment 1

> Username: mglisse  
> Created at: 2019-01-22 17:05:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/108#issuecomment-456479294  

I forgot to mention that I can do  
```c++  
double d = x.convert_to<double>();  
int i = x.compare(d);  
```  
and use `i` to know how to use `nextafter`, which is a clean enough workaround, but I think it is essentially doing the work twice.
