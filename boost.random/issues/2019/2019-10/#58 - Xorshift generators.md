# #58 - Xorshift generators [Open]

> Username: heitorPB  
> Created at: 2019-10-13 15:12:18 UTC  
> Updated at: 2019-11-04 19:14:01 UTC  
> Url: https://github.com/boostorg/random/issues/58  

I found the [Xorshift](https://en.wikipedia.org/wiki/Xorshift) Wikipedia page and saw that Boost doesn't have Marshaglia's nor Vigna's generators.  
  
Is there a reason for that? If not, I'd like to request those :) I'd like to help adding them to Boost (if that's the case), but keep in mind that I'm not an expert in PRNGs.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-10-14 15:37:09 UTC  
> Url: https://github.com/boostorg/random/issues/58#issuecomment-541752258  

AMDG  
  
On 10/13/19 9:12 AM, Heitor wrote:  
> I found the [Xorshift](https://en.wikipedia.org/wiki/Xorshift) Wikipedia page and saw that Boost doesn't have Marshaglia's nor Vigna's generators.  
>   
> Is there a reason for that? If not, I'd like to request those :) I'd like to help adding them to Boost (if that's the case), but keep in mind that I'm not an expert in PRNGs.  
>   
  
You should talk to @degski.  I believe that his implementation  
is complete and just needs a bit of boilerplate for integration.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: degski  
> Created at: 2019-10-15 05:40:24 UTC  
> Url: https://github.com/boostorg/random/issues/58#issuecomment-542045950  

I'm literally heading for the door for an international trip. I need to remove some things (`xoroshiro128plusshixo`, `xoroshiro128plusshixostar` and `xoroshiro128plusshixostarshixo` (see [readme](https://github.com/degski/xoroshiro#xoroshiro128plusshixo--xoroshiro128plus--finalizer--r--32---r--))) from the implementation, then, as far as I can see it's good to go. As a bonus, it provides `splitmix64`, which is recommended by Vigna for seeding (so, I implemented it that way). I'll get back to it later.

---

## Comment 3

> Username: heitorPB  
> Created at: 2019-10-15 13:47:14 UTC  
> Url: https://github.com/boostorg/random/issues/58#issuecomment-542220383  

That's great, @degski!  
  
I quickly saw your code and noticed you implemented only the Xoroshiros. Is there a reason for not including the xoshiros? I'm particularly interested in xoshiro256** (and curious about the the variantes with 512 and 1024 bits of space).

---

## Comment 4

> Username: heitorPB  
> Created at: 2019-11-04 19:14:01 UTC  
> Url: https://github.com/boostorg/random/issues/58#issuecomment-549504033  

ha, sorry @degski , I didn't look enough in your code previously.. all those generators are in your code. How can I help to add your code to Boost?
