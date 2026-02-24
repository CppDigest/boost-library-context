# #240 - integral_constant does not model Euclidean Ring [Open]

> Username: henrique-almeida  
> Created at: 2016-01-17 07:52:33 UTC  
> Updated at: 2017-02-12 23:07:59 UTC  
> Url: https://github.com/boostorg/hana/issues/240  

consider this:  
BOOST_HANA_CONSTANT_CHECK(hana::uint_c<1u << 31> \* hana::uint_c<2> == hana::uint_c<0>);  
  
(p.s.: i love you and your library (nohomo))

---

## Comment 1

> Username: ldionne  
> Created at: 2016-01-18 22:01:57 UTC  
> Url: https://github.com/boostorg/hana/issues/240#issuecomment-172666172  

Hmm, actually the problem here is that overflow is defined to wrap for unsigned integral types. Because if you say for example `hana::int_c<1 << 31> * hana::int_c<2>`, then you get a compile-time error saying the result is not representable in an `int`.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-18 22:12:51 UTC  
> Url: https://github.com/boostorg/hana/issues/240#issuecomment-172668068  

It would be very unfortunate if we were unable to provide `%` and `/` for unsigned integral types... But at the same time the laws are not respected as-is, which is not acceptable. Do you have a resolution in mind?
