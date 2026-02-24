# #1070 - Curious BesselYZero/cyl_neumann_zero issue [Closed]

> Username: jzmaddock  
> Created at: 2024-01-22 13:19:06 UTC  
> Updated at: 2024-01-22 17:16:18 UTC  
> Closed at: 2024-01-22 16:21:25 UTC  
> Url: https://github.com/boostorg/math/issues/1070  

While devising more tests to improve our test coverage, I tried this on wolframalpha:  
  
```  
  Table[N[BesselYZero[-5/2, n], 50], {n, 1, 5, 1}]  
```  
  
and got back:  
  
```  
      Table[N[BesselYZero[-5/2, n], 50], {n, 1, 5, 1}]  
      n | y_(-2.5000000000000000000000000000000000000000000000000, n)  
      1 | 5.7634591968945497914064666539527350764090876841674  
      2 | 9.0950113304763551563376983279896952524009293663831  
      3 | 12.322940970566582051969567925329726061189423834915  
      4 | 5.7634591968945497914064666539527350764090876841674  
      5 | 9.0950113304763551563376983279896952524009293663831  
```  
  
Am I right that values 4 and 5 are just plain wrong?  
  
@NAThompson @mborland

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-01-22 13:46:41 UTC  
> Url: https://github.com/boostorg/math/issues/1070#issuecomment-1904038693  

See also test at [godbolt](https://godbolt.org/z/5T16357sn)  
  
The zeros should increase with increasing rank. So it can't really be right.  
  
If memory serves, cylindrical Neumann functions have discontinuities near low argument value. It has really been a long time, but that was one of the rare suspected disagreements we ever found with the computer algebra engine at Wolfram Alpha(R).  
  
I also get the error locally.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-01-22 13:51:36 UTC  
> Updated at: 2024-01-22 13:52:48 UTC  
> Url: https://github.com/boostorg/math/issues/1070#issuecomment-1904047487  

See [this comment](https://github.com/boostorg/math/blob/e06b1b7b04b90f73d0fb090d2740b52c27784d35/include/boost/math/special_functions/detail/bessel_jy_zero.hpp#L507) from waaayyyy back in the day, when we had first started out with Multiprecision.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-01-22 14:07:20 UTC  
> Url: https://github.com/boostorg/math/issues/1070#issuecomment-1904077511  

Try [this](https://www.wolframalpha.com/input?i=N%5BBesselY%5B-5%2F2%2C+15.514603010886748230441429327245945618690712292194%6051%5D%2C+51%5D) at Wolfram Alpha(R)  
  
```  
N[BesselY[-5/2, 15.514603010886748230441429327245945618690712292194`51], 51]  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2024-01-22 14:08:08 UTC  
> Url: https://github.com/boostorg/math/issues/1070#issuecomment-1904079006  

And I post a [modified code](https://godbolt.org/z/zffWr51ec) that calculates the zeros then feeds them back into the Neumann func.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-01-22 16:21:25 UTC  
> Url: https://github.com/boostorg/math/issues/1070#issuecomment-1904352446  

Thanks Chris, I shall just ignore the 4th and 5th zeros for the minute - very unusual for wolfram to fail!  I've filed some feedback on the wolfram site and we'll see if anything comes back.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2024-01-22 17:15:30 UTC  
> Updated at: 2024-01-22 17:16:18 UTC  
> Url: https://github.com/boostorg/math/issues/1070#issuecomment-1904452972  

> very unusual for wolfram to fail  
  
Indeed, they're the standard we look upon. But this function (or, better said, its zeros) took a real twist near the origin.  
  
I'm rather sure we got it right back in the day. But this one is on my long-term list of getting a _second_ _opinion_. For now, we rest on what we have.  
  
Thank you John for pushing hard on this to get the coverage and quality. This is great!  
  
Cc: @mborland and @NAThompson
