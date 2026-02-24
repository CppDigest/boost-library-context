# #737 - Issue in sin/cos_pi: [Closed]

> Username: jzmaddock  
> Created at: 2022-01-08 18:32:05 UTC  
> Updated at: 2022-01-11 19:21:44 UTC  
> Closed at: 2022-01-11 19:21:44 UTC  
> Url: https://github.com/boostorg/math/issues/737  

Lurking in my inbox:  
  
> I sent a long long time ago (for boost 1.72) a proposal of correction of a very small bug of the cos_pi and sin_pi implementations  
>   
> and I have just see that in the last 1.78 beta 1 nothing has been corrected yet.  
>   
> The bug is from a conversion, line 36 for cos_pi.hpp and line 41 for sin_pi.hpp:  
>   
>    if(iconvert(rem, pol) & 1)  
>       invert = !invert;  
>   
> if rem is not representable in the integral type issued by iconvert, the result can be  incorrect.  
>   
> I proposed a a one liner correction, replacing the wrong test by  
>   
>    if(floor(rem/2)*2 != rem)  
>       invert = !invert;  
>   
> which does not make use of integer conversion.  
>   
> Of course any other way to correctly compute the parity of rem will do the job.  
>   
> Looking forward for your answer and correction perhaps for boost 1.79 ?  
>   
> Thanks for the great boost::math, and apologies for this direct mailing.  
>   
>     Jean-Thierry Lapresté

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-01-11 19:21:44 UTC  
> Url: https://github.com/boostorg/math/issues/737#issuecomment-1010285641  

Fixed in referenced PR
