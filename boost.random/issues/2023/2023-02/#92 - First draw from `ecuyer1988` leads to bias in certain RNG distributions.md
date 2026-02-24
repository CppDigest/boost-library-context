# #92 - First draw from `ecuyer1988` leads to bias in certain RNG distributions [Closed]

> Username: WardBrian  
> Created at: 2023-02-24 16:30:17 UTC  
> Updated at: 2023-05-13 18:17:55 UTC  
> Closed at: 2023-05-13 18:17:55 UTC  
> Url: https://github.com/boostorg/random/issues/92  

This was first noted in the Stan project in https://github.com/stan-dev/stan/issues/3167  
  
When using `ecuyer1988` as an engine and a "small" seed (less than about 10,000), the first draw of the `poisson` and `uniform` distributions is biased high. For example, see:   
- Poisson: https://godbolt.org/z/G953v67c4  
- Uniform(0,1): https://godbolt.org/z/ePev6fss3  
  
The behavior seems to improve as you increase the size of the seed, but is still noticeable even for very large seeds (e.g. those drawn form `uniform(10_000,1_000_000)`. In the Poisson distribution, the behavior seems to go away if you have a rate greater than 10

---

## Comment 1

> Username: kotika  
> Created at: 2023-02-24 18:56:32 UTC  
> Url: https://github.com/boostorg/random/issues/92#issuecomment-1444262474  

> On Feb 24, 2023, at 18:30, Brian Ward ***@***.***> wrote:  
> This was first noted in the Stan project in stan-dev/stan#3167 <https://github.com/stan-dev/stan/issues/3167>  
> When using ecuyer1988 as an engine and a "small" seed (less than about 10,000), the first draw of the poisson and uniform distributions is biased high.   
>   
  
  
Similar defects with obsolete ancient RNGs have been reported time and time again. In boost, the modern, industrial strength RNG is the MIXMAX.  
Along with sequences indistinguishable from true random numbers, attention has been paid to seeding, such that the sequence   
obtained from seed(1) starts out perfectly randomized and is completely independent from seed(2), seed(3) etc, including the case if you   
want to test the quality by interleaving the two or more streams.  
  
Another defect, reported for obsolete RNGs is the lack of fine resolution. Most or all of the old 32-bit RNG will no produce numbers closer to each other  
than about 10^-9, although afaik this issue is supposed to have been taken care of with the use of std::random distributions.  
  
Cheers,  
Kostas  
  
=========================================  
Institute of Nuclear and Particle Physics  
NCSR Demokritos  
http://inspirehep.net/author/profile/K.G.Savvidy.1  
https://mixmax.hepforge.org

---

## Comment 2

> Username: WardBrian  
> Created at: 2023-02-24 19:09:34 UTC  
> Url: https://github.com/boostorg/random/issues/92#issuecomment-1444286915  

For our use case it is important to be able to discard a huge number efficiently, so options like MINMAX are not very suitable for us

---

## Comment 3

> Username: swatanabe  
> Created at: 2023-02-24 23:15:14 UTC  
> Url: https://github.com/boostorg/random/issues/92#issuecomment-1444655046  

On 2/24/23 12:09, Brian Ward wrote:  
> For our use case it is important to be able to discard a huge number efficiently, so options like MINMAX are not very suitable for us  
>   
  
Theoretically, most generators support fast discard. It's rarely   
implemented for anything that isn't an LCG or LFSR, though, as it  
can be quite complex.  I never really looked at the mathematical  
structure of MIXMAX, so I have no idea whether a fast discard is  
possible for it. I did implement optimized discard for mt19937.

---

## Comment 4

> Username: swatanabe  
> Created at: 2023-02-24 23:19:59 UTC  
> Url: https://github.com/boostorg/random/issues/92#issuecomment-1444675693  

On 2/24/23 09:30, Brian Ward wrote:  
> In the Poisson distribution, the behavior seems to go away if you have a rate greater than 10  
>   
  
That's not surprising: poisson_distribution has two independent   
implementations with the cutoff being 10.

---

## Comment 5

> Username: swatanabe  
> Created at: 2023-02-24 23:27:34 UTC  
> Url: https://github.com/boostorg/random/issues/92#issuecomment-1444693687  

On 2/24/23 11:56, Kostas Savvidis wrote:  
>   
>> On Feb 24, 2023, at 18:30, Brian Ward ***@***.***> wrote:  
>> When using ecuyer1988 as an engine and a "small" seed (less than about 10,000), the first draw of the poisson and uniform distributions is biased high.  
>>  
>   
>   
> Similar defects with obsolete ancient RNGs have been reported time and time again. In boost, the modern, industrial strength RNG is the MIXMAX.  
  
I agree with Kostas here.  ecuyer1988 is not a very high quality PRNG.   
On top of that, most older PRNGs provide no distribution guarantees   
whatsoever for the usage you're describing.

---

## Comment 6

> Username: kotika  
> Created at: 2023-02-27 12:05:28 UTC  
> Url: https://github.com/boostorg/random/issues/92#issuecomment-1446210886  

On Sat, 25 Feb 2023 at 1:27 AM, swatanabe ***@***.***> wrote:  
  
> On 2/24/23 11:56, Kostas Savvidis wrote:  
> >  
> >> On Feb 24, 2023, at 18:30, Brian Ward ***@***.***> wrote:  
> >> When using ecuyer1988 as an engine and a "small" seed (less than about  
> 10,000), the first draw of the poisson and uniform distributions is biased  
> high.  
> >>  
> >  
> > Similar defects with obsolete ancient RNGs have been reported time and  
> time again. In boost, the modern, industrial strength RNG is the MIXMAX.  
>  
> I agree with Kostas here. ecuyer1988 is not a very high quality PRNG.  
> On top of that, most older PRNGs provide no distribution guarantees  
> whatsoever for the usage you're describing.  
  
  
 I suspect that the authors of Stan implemented seeding several parallel  
streams/threads (or “chains” in MCMC lingo) using discard. Some while ago  
another user of Monte-Carlo had been asking me for arbitrary skip in  
MIXMAX. But this is a misunderstanding. When a user needs parallel streams  
from MIXMAX they should just seed using the default method from consecutive  
seeds. If you need eight streams you can seed them with 1,2,3…8 or with  
1001,1002,1003,…1008. No other generator currently gives you that kind of  
satety.  
  
Cheers,  
Kostas  
  
.
