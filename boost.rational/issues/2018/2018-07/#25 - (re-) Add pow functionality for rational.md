# #25 - (re-) Add pow functionality for rational [Open]

> Username: jeking3  
> Created at: 2018-07-14 15:22:23 UTC  
> Updated at: 2018-08-01 16:25:16 UTC  
> Url: https://github.com/boostorg/rational/issues/25  

See: https://github.com/boostorg/rational/pull/24  
  
In 2015 a pull request added pow() and reciprocal().  In 2018 it was reviewed by CMT and reciprocal() was found to be unnecessary.  This left boost::pow() for rational, however it introduced an issue resolving std::pow from within boost namespaces.  This happened close to the 1.68.0 release (during beta) so it was removed.  
  
Originally recommend looking at adding `operator ^=` as a modifying operator to raise the rational value to an integer exponent.  In addition `operator ^` could take an existing rational and raise it to an exponent, returning a new rational. - note comments below.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-07-14 15:33:26 UTC  
> Url: https://github.com/boostorg/rational/issues/25#issuecomment-405030829  

AMDG  
  
On 07/14/2018 09:22 AM, James E. King III wrote:  
> <snip>  
> Recommend looking at adding `operator ^=` as a modifying operator to raise the rational value to an integer exponent.  In addition `operator ^` could take an existing rational and raise it to an exponent, returning a new rational.  
>   
  
  
I strongly disagree with this as operator^ means something  
completely different and has the wrong precedence.  The correct  
name is pow, as long as the issues are dealt with.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created at: 2018-07-14 15:38:46 UTC  
> Url: https://github.com/boostorg/rational/issues/25#issuecomment-405031107  

I was thinking the commonly used mathematic notation of (x^2) would be reasonable here, however the precedence issue is interesting.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-08-01 13:22:07 UTC  
> Url: https://github.com/boostorg/rational/issues/25#issuecomment-409572451  

I think perhaps just moving pow into another namespace (ideally boost::rational) would resolve the issue sufficiently and avoid the collision that was happening.

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-08-01 16:25:16 UTC  
> Url: https://github.com/boostorg/rational/issues/25#issuecomment-409634692  

AMDG  
  
On 08/01/2018 07:22 AM, James E. King III wrote:  
> I think perhaps just moving pow into another namespace (ideally boost::rational)  
  
  boost::rational is a class template.  The idea is  
sound in principle, but pow does need to be found  
by ADL.  I suppose that you could just document  
that pow lives in an unspecified associated namespace  
of rational.  
  
> would resolve the issue sufficiently and avoid the collision that was happening.  
>   
  
In Christ,  
Steven Watanabe
