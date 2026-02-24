# #336 - How to bin integral vs floating point correctly (precision issue and general question) [Open]

> Username: emmenlau  
> Created at: 2021-09-27 08:56:56 UTC  
> Updated at: 2023-06-14 18:51:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/336  

Dear @HDembinski and other authors, thanks for this awesome library. We have just started using it and the speed and versatility are impressive! We have a problem that manifests as a precision problem, but also shows a more general question.  
  
The problem: We use the floating precision histogram to bin any kind of data. When we bin data (gray value camera images) of integer precision, we set lower and upper bound based on the data range to `[min, max+1]` and the number of bins to `upper bound - lower bound`. However there are sometimes cases where the numeric precision then leads to an empty last bin. This is due to rounding error, the values fall into `max-1` instead of `max`. We checked and the bin index is then computed as `(max-1).999...` which finally resolves to `max-1`. Is there a good solution to this? It leads to confusion that the last bin is empty, and its also a bit problematic in writing tests.  
  
The underlying question: We are not completely certain what is a "recommended" way to set histogram bounds for integral data space. Are there common guidelines? In floating point precision it is more understandable what data range is covered by each bin. But in integral space, it seems the value `0` could be best represented by bin `[-0.5, 0.5)` rather than the more obvious `[0, 1)`. And help, links or pointers to documentation would be highly appreciated!

---

## Comment 1

> Username: HDembinski  
> Created at: 2021-09-27 11:00:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927752904  

Thanks for the kind praise!  
  
This is a recurring question and the standard answer is to use an `integer` axis for images. The `regular` axis is intended for real-valued input values from a continuous distribution, not for integer values.  
  
So would using `boost::histogram::axis::integer` work for you? The integer axis uses exact integer arithmetic, and it is a bit faster than the `regular` axis, too.  
  
If for some reason your input values are really floating point, you could add half the bin width to avoid the rounding errors. This is not an elegant solution, but it works.  
  
This is also not an elegant solution, but you can even make your own axis class that does this step automatically. See https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_axes for examples.  
  
Basically, you would override the`index` method of the regular axis, just like in the first example and add half the bin width to the input.

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-09-27 11:02:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927754737  

Come to think of it, you could also override the `index` method with something that rounds to the nearest integer.

---

## Comment 3

> Username: HDembinski  
> Created at: 2021-09-27 11:12:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927764038  

The correct solution for this is for us to support `boost::rational` as the value type of the regular axis, see https://www.boost.org/doc/libs/1_77_0/libs/rational/rational.html, which would enable exact rational arithmetic. This does not work currently, but I can look into it.

---

## Comment 4

> Username: emmenlau  
> Created at: 2021-09-27 11:14:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927766581  

Thanks a lot @HDembinski for the help!  
  
What you say makes sense. We just looked at the integer axis, and it most likely would help. But I understand it can not be used with arbitrary bin counts, correct? This is not a huge impediment but it would be a bit sad.  
  
Also the suggestion to add half a bin width would seem feasible and possibly a good idea! But then I guess we could not use the range-based `fill()` anymore which gives a very noticeable speedup?  
  
Support for `boost::rational` sounds awesome, but would it introduce a significant slow-down?

---

## Comment 5

> Username: emmenlau  
> Created at: 2021-09-27 11:17:43 UTC  
> Updated at: 2021-09-27 11:17:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927772333  

Can I also ask a related question, in case you know: from a completely different point of view, is it sensible (or common) to shift the bin bounds by half a bin width down? We where considering this idea that for example bin `0` would have bounds `[-0.5, 0.5)` (for the example bins of size one). We see some potential benefits that (a) the integer or floating value falls in the center of the bin rather than the border, and (b) it compensates for aforementioned rounding issues. But we do not feel confident about this route if it is a very uncommon thing to do...

---

## Comment 6

> Username: HDembinski  
> Created at: 2021-09-27 11:19:19 UTC  
> Updated at: 2021-09-27 11:23:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927774530  

> What you say makes sense. We just looked at the integer axis, and it most likely would help. But I understand it can not be used with arbitrary bin counts, correct? This is not a huge impediment but it would be a bit sad.  
  
That is correct, `axis::integer` assumes that the "bin width" is 1. Perhaps we need another axis which allows integral multiples? What exactly do you need?  
  
> Also the suggestion to add half a bin width would seem feasible and possibly a good idea! But then I guess we could not use the range-based fill() anymore which gives a very noticeable speedup?  
  
If you make your own axis which implements this hack, it will work with range-based fill().  
  
> Support for boost::rational sounds awesome, but would it introduce a significant slow-down?  
  
I don't know, but I don't think the speed penalty would be terrible. It is not hard to implement, I think, so we should really support this and then I can check the benchmarks.  
  
Yet another option that I forgot is to use the `axis::variable`, which you can construct in such a way that the bin edges are exactly where you want them to be. `axis::variable` is considerably slower than `axis::regular`, though.

---

## Comment 7

> Username: emmenlau  
> Created at: 2021-09-27 11:25:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927781974  

Thanks a million for this fast and very helpful reply. I need to digest this new input a bit. Support for `boost::rational` sounds awesome, but your input has triggered new thoughts that require a bit more discussion on our side...

---

## Comment 8

> Username: HDembinski  
> Created at: 2021-09-27 12:29:23 UTC  
> Updated at: 2021-09-27 12:32:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-927826155  

Once again thinking more about it and reading your original message again, is it correct that you only have an issue with the very last bin edge? Because then your need may be resolved if we implement #319  
  
Boost.Histogram by default gives you under and overflow bins for each axis, and then the interval you specify is semi-open [a, b). This makes sense if your input is real-valued and drawn from a continuous distribution. In your case, you probably want a closed interval [a, b]. I plan for a while to support this if the user creates a regular axis with the overflow bin turned off.

---

## Comment 9

> Username: emmenlau  
> Created at: 2021-10-01 11:19:50 UTC  
> Updated at: 2021-10-01 13:15:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-932142725  

Sorry for the delay in feedback. We've just tested #344 and it does solve a corner case for us, thanks for this. However the original problem still remains because we commonly do use overflow bins.  
  
> ... is it correct that you only have an issue with the very last bin edge?  
  
We have not observed the problem with other bins yet. But I am under the impression that this is just by chance. The problem should happen with any bin, when just by chance the raw index position falls by a margin below the lower bound of the "correct" bin.  
  
We've discussed this internally and can't make up our minds between the multiple possible workarounds. What I see so far:  
 - using boost::rational: This seems very powerful and would certainly address the problem in a clean way. But it seems this also makes significant work in the implementation. Also, I could not find any benchmarks on boost::rational that would help to estimate the potential performance hit(?) It would be quite sad if it incurs a significant performance overhead.  
 - adding an epsilon of half a bin width in the computation of the index: This seems the easiest solution for us that should solve the problem in a generic way. I haven't tested, but the performance hit may also be negligible compared to the division. But its not entirely clear to me how this would be implemented in a clean way. If boost::histogram would handle integer binning internally with epsilon, and floating point binning without epsilon, it may add an ambiguity where the same value in different types is binned differently. On the other hand, if this is not handled inside boost::histogram, then how could we continue to use the range-based fill methods (that give the significant speed boost)?

---

## Comment 10

> Username: emmenlau  
> Created at: 2021-10-01 13:25:01 UTC  
> Updated at: 2021-10-04 06:36:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-932226205  

After a longer discussion we are considering a third option, that I wanted to get your feedback on. The best of all worlds for us seems to be an axis that supports both integer and floating point and can automatically switch between the two. It should behave like the integer axis, as long as the bounds and the scale are (precisely) integral values. In this case, it correctly bins integer and floating point values by using the precise (and faster) integer arithmetics. Only when any of the relevant parameters (bounds or scale) is set to floating point, it automatically switches to floating point arithmetics.  
  
This seems to bring a set of nice benefits, and we could not see any downsides (yet). Benefits:  
 - A single histogram instance gives the "perfect" integer arithmetics precision when legal, but also supports floating point arithmetics when needed. No need for the user to decide upfront. And it significantly reduces the risk of rounding errors.  
 - In many cases, this can give superior speed even for floating point histograms, just as long as their parameters coincide with integral parameters.  
  
Did we overlook some obstacles here? What do you think?

---

## Comment 11

> Username: emmenlau  
> Created at: 2022-01-10 09:04:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1008665728  

Hi @HDembinski , sorry to bother you with this question again, but it would be great to get your feedback on the above implementation idea. We've implemented such an axis as a test (an axis that supports both integer and floating point precision, and automatically switches between the two, depending on the user parameters).  
Does that seem reasonable to you? And would that be something that boost::histogram could support native (rather that our test implementation)?

---

## Comment 12

> Username: HDembinski  
> Created at: 2022-01-10 09:32:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1008688230  

Sorry for not responding earlier. I think as a practical fix, it is fine to just shift the bins by half a bin width  if you are still considering this.  
  
The axis that you mention, which uses exact integer arithmetic when everything is integral: this is already how floating point numbers with integral values work to my knowledge. Could you perhaps produce a small code example to show the wrong behavior and the expected?

---

## Comment 13

> Username: HDembinski  
> Created at: 2022-01-10 09:36:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1008691026  

In any case, if you can make a prototype for such an axis with these properties, I am happy to review and build a PR to include it.  
  
That being said, I still think that the clean solution is to support boost::rational in the regular axis. I don't think that the performance hit is terrible, but we have to benchmark it.  
  
Thanks for your endurance in trying to work out a solution.

---

## Comment 14

> Username: HDembinski  
> Created at: 2022-01-10 13:06:57 UTC  
> Updated at: 2022-01-10 13:14:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1008856162  

Prototype of an axis that works with boost::rational:  
https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGEgMykrgAyeAyYAHI%2BAEaYxCAAbKQADqgKhE4MHt6%2BASlpGQKh4VEssfFJdpgOmUIETMQE2T5%2BXIFVNQJ1DQTFkTFxibb1jc25bcM9faXliQCUtqhexMjsHOb%2BYcjeWADUJv5u0ahpBAD0CHgKRMDErAB0CMnJB9gmGgCCG1s7mPuHx1OFyuNzuLDOp2ImAeTxe/jen2%2BDG2Xj2ByOJ2uZzujgEYkez1e7y%2BZk2yN%2B/zcTmuUNYRMRnzCBF2LCYYQgc32AHYrJ9dvzdsw2ApkkxVrtogh/gARCWYgggECXa6oW50/y8j4C3ZedJGXY4mVy06KnGZMToplEjXE4namleBy7JiqK7czXa7W6sLAXYANzEXkwAH0CABPZJ/A6ynEHD2e/ne/VhLCqUMRqP%2BWWSxUuq6KlOYNPhyNx218hO7QvFjOCnzBssVhMB7whkt/VBUKhKAgNm1Nz0toPpyO7BSieh9zV2yt5hQQasjv4MUj%2BwNt2tMVdDjej6KckAzysChj1kw8hjn6WrzvdzC988WJhX1fjsQhx%2Bxi9X3ZnXYQaJdgAWmdOYf0fK9yy1StF3bKsGFTCA0AYa411bJdzASXZVE5ZDUIggdjzw5kmC8IgqyNQFrlNJhcWYWhg1Ea5LQQosMPhCA30nXYACp/1UYDdlvHtgzmMD%2B2g49%2BShAhlgYCjrVlICuH%2BAAxf88EpOsWGDNSKJAbTRN2AzlMbSTPUghlzO1HclzQoMF1YmtRzwXCBHwnkjykmS5Iov8uJDfZLCErsRLM49LK%2BQiBVg2t0gAL0wDldmI90DXvXzTx0uNuWlKDj2uWi8GQFL3IIItkmIZ0yNQITkjo%2BdOUfdLZOIeScxAOdFVQerMkVLxWOIKhaFQAB3bk3AlJBOtdBRut65wQFQP04mGsacsi7VIIkrzSPIqVoymxVWQAaxDZVQVYCA50fLgNFXe7dgAVivcTpwHBAIGUt6vM%2Brgfo%2BiAzABqLzOudBFTQMjKXRXYDsOWHwcVVxaDMq8OAWWhOCe3g/A4LRSFQTg3GsawxyWFYo1JHhSAVfGMYWE6QCegAOe4AE4uQ0SQuQSdmuCkfwNC5aQsY4SRcc0XgiY4Xg5vuumtAWOBYBgRAUFQFhkjoOJyEoNAtZ1%2BJtkMYABY0Lg%2BDocriDmgCpdIaIwgaMNOBpg22EEAB5BhaFd%2BnSCwVkjHEAP8ChGoVrmgOi2qMi1gJplMDFgnaDwaI7mIMMPCwN3eAIYg8BYPOFmGphgAUAA1PBMFGr3Izxmn%2BEEER3wkaRm/kJQ1Ad3RLYMIwUFJyx9HTubYCFdgqIIUgVvibguS5BnCYWlDODl5PqjolwELGVoggQ6YBniS3UnSOi970M/CgYI%2BykGS2Ojo7pRk8Fo9Cf2oRl6MJ%2Bnvk/JivxyPvQqjQ76zH%2BosZYqw9AF0wGsHgmNsaSwDjLVQLMEhAQSJIFKA9fQC3uBoe4KkIAk0sNYVcuBCAkCCv4S2uwPCG3oFVDY/185SzEqQJmT17piwlqQYukhJCEK4CzSQ3N2bC05k9SQSQ8YExlnLEACsOGkBVurJYBBkhkT1khTW2tmF6EwPgIgADO6t3EFIGQghFAqHUAHUadxkgl30Mg0g8jpacC9mRbRzJOzYQwVgnBJt9QEKISQxhBi4i0LYbTDhCwEDQiwPEDkrjxa8GLk9J6HMWZcCeuzHh7MzAaDMJIGR7iHaKNsMouJ9NOFM0kFwQhZgCmkg0CzFmT0%2BZmCSGLfwKCFHr1qUrJBHAzADM8bLYZy857pGcJIIAA

---

## Comment 15

> Username: emmenlau  
> Created at: 2022-01-10 14:03:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1008902688  

Awesome! I'll take a look at it in the evening, thanks a lot already!

---

## Comment 16

> Username: HDembinski  
> Created at: 2022-01-12 11:28:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1010943846  

@emmenlau Could you please post a minimal example that shows the bug you experiencing? What I said earlier, that integer arithmetic with doubles is exact to my knowledge, is only true when no fractions are computed in intermediate results. I would like to understand your issue better. Consider these two mathematically identical calculations of an index from a value:  
```  
index1 = ((x - a) * n) / (b - a)  
index2 = (x - a) / ((b - a) / n)  
```  
These two calculations, when calculated with floating point types, are not giving the exact same result even when x, a, b, n are integer values. In my understanding, however, at least the first index1 should give a result identical to a calculation with integer types. I need to test this, though.

---

## Comment 17

> Username: HDembinski  
> Created at: 2022-01-12 11:57:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1010970662  

axis::regular currently uses yet another version of this calculation:  
```  
index3 = n * ((x - a) / (b - a))  
```  
https://github.com/boostorg/histogram/blob/bf7712f0d7b548f5f29487a18aa1ae9f221e08ad/include/boost/histogram/axis/regular.hpp#L330  
  
This calculation also uses non-integer values as intermediate results, so it is not expected to give the exact same result as an integer calculation even when x, a, b, n are integer values.

---

## Comment 18

> Username: HDembinski  
> Created at: 2022-01-12 12:02:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1010974773  

For reference, numpy.histogram uses a rather expensive algorithm to handle values which end up exactly on bin edges:  
https://github.com/numpy/numpy/blob/e2d35064df262efa6eb7dfe5bfc43160c73cf685/numpy/lib/histograms.py#L857

---

## Comment 19

> Username: HDembinski  
> Created at: 2022-01-12 13:00:26 UTC  
> Updated at: 2022-01-12 14:16:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1011020612  

Here is a little numerical test. It looks like the calculation that axis::regular uses is the worst of the aforementioned alternatives. Surprisingly, index2, index4, and index5 work the best. index5 is the best version, because it avoids the division in the hot calculation, which is slower than multiplication.  
```py  
import numpy as np  
  
rng = np.random.default_rng(1)  
  
for i in range(10):  
    info = np.iinfo(np.int32)  
    # for large ranges we also get round-off errors in index2  
    a = rng.integers(info.min // 2, info.max // 2, size=1000000)  
    b = rng.integers(info.min // 2, info.max // 2, size=1000000)  
  
    m = a < b  
    a = a[m]  
    b = b[m]  
    n = b - a  # bin width is 1  
  
    # cast to integer rounds toward 0, not down, so the formulas below  
    # are only correct for x >= a  
    x = rng.integers(a, b + 1, size=len(a))  
  
    # reference, uses integer calculation  
    index0 = (x - a) * n // (b - a)  
  
    x = x.astype(float)  
    a = a.astype(float)  
    b = b.astype(float)  
    index1 = (((x - a) * n) / (b - a)).astype(np.int64)  
    index2 = ((x - a) / ((b - a) / n)).astype(np.int64)  
    index3 = (((x - a) / ((b - a))) * n).astype(np.int64)  
    index4 = ((x - a) * (1.0 / ((b - a) / n))).astype(np.int64)  
    index5 = ((x - a) * (n / (b - a))).astype(np.int64)  
  
    m = [  
        index0 != index1,  
        index0 != index2,  
        index0 != index3,  
        index0 != index4,  
        index0 != index5,  
    ]  
  
    for j, mj in enumerate(m):  
        if np.any(mj):  
            print(f"index{j+1}", x[mj][0], a[mj][0], b[mj][0])  
```  
this returns  
```  
index1 70540990.0 20392249.0 765891453.0  
index3 148926560.0 -101619068.0 490728093.0  
index1 592366157.0 224185664.0 1025950092.0  
index3 568757840.0 554782207.0 911259799.0  
index1 567985439.0 -372272012.0 1043697140.0  
index3 342054863.0 -716197359.0 632989928.0  
index1 -24123696.0 -356486757.0 540167239.0  
index3 391566084.0 -241532021.0 991591579.0  
index1 -605887583.0 -710964237.0 -468796994.0  
index3 517667650.0 -498733954.0 1049781459.0  
index1 18204388.0 -410639210.0 49916775.0  
index3 -660202220.0 -891467850.0 879697083.0  
index1 -534798388.0 -780387129.0 -465835569.0  
index3 83679015.0 -339367697.0 405706973.0  
index1 652100293.0 463874896.0 694011684.0  
index3 -72010471.0 -126660641.0 -23248043.0  
index1 314307906.0 -636650940.0 660664605.0  
index3 186928578.0 -555640498.0 810504453.0  
index1 144217769.0 -329635903.0 346199237.0  
index3 144217769.0 -329635903.0 346199237.0  
```

---

## Comment 20

> Username: HDembinski  
> Created at: 2022-01-12 14:19:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1011092024  

That index2, index4 and index5 give the same answers is not a surprise, since by construction (b-a) / n == 1.0.

---

## Comment 21

> Username: vakokako  
> Created at: 2022-01-13 09:58:29 UTC  
> Updated at: 2022-01-13 09:58:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1011976043  

@HDembinski thanks for all your input, here is an example of the problem we are experiencing: [godbolt](https://godbolt.org/z/oaPo6n17h).

---

## Comment 22

> Username: emmenlau  
> Created at: 2022-01-13 10:15:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1011990791  

@HDembinski your input and discussion is very enlightening, thanks! As @vakokako shows, the problem for us typically arises when using the axis with larger integral bounds, that cause small rounding errors. If a bin in the middle of the histogram is affected (as in @vakokako's example) the problem is not so visible. But in other, more common cases, the last bin can be affected, and it will remain completely empty. This causes typically some confusion with our users. Is this a suitable motivation for the issue?  
  
When further looking into this, @vakokako found that we can use the more precise integral arithmetic when:  
 - The difference `d` between upper and lower bound is (precisely) integral, and  
 - The difference `d` is a multiple of the histogram size.

---

## Comment 23

> Username: vakokako  
> Created at: 2022-01-13 10:19:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1011993541  

Here is our solution axis to this: [godbolt](https://godbolt.org/z/f8fcrd6rd).  
  
This is achieved by adding a bool flag `mIsIntegral` and changing the  
computation of index if we now that the axis is `integral`, otherwise  
forward to `regular` axis computation.  
  
Example of axes where `mIsIntegral == true`:  
- regular_precise(10, -5, 5)  
- regular_precise(5, -5, 5)  
- regular_precise(10, -5.5, 4.5) - shifting of bounds is also supported  
  
Example of axes where `isIntegral() == false`:  
- regular_precise(11, -5, 5)  
- regular_precise(20, -5, 5)  
- regular_precise(10, -5, 5.5)  
  
The additional members for lower/upper bounds are not neccessary, they are there only cause we wanted to keep the dependency on `axis::regular` and it stores those as private members.

---

## Comment 24

> Username: Dominic-Stafford  
> Created at: 2022-08-02 16:34:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1202965497  

Hi, I was sent here from this issue by @henryiii from the above issue, and just wanted to ask if you anticipate being able to implement one of the fixes discussed here at some time soon? My issue just comes from integer values in the middle of the distribution falling on the wrong side of integer bin edges, so I think either the solution with boost::rational or a change in the calculation as you discussed should fix this.

---

## Comment 25

> Username: emmenlau  
> Created at: 2022-08-02 17:59:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203045258  

Hi @Dominic-Stafford ! We have an implementation based on what you find in the godbolt-link above and it works quite well for us, all problems with precision are obsolete. However @HDembinski is much more knowledgeable about the possible pro's and con's with respect to i.e. the solution with `boost::rational`. What I can say is that we observe a tiny slowdown in benchmarks with our solution compared to the upstream code, which is surprising since we use integer math in the relevant case. I assume that `boost::rational` would be even slower, but again @HDembinski may know this much better.

---

## Comment 26

> Username: HDembinski  
> Created at: 2022-08-02 22:17:17 UTC  
> Updated at: 2022-08-02 22:19:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203267833  

~Hi all, I am still confused why you cannot use an integer axis, because that should be faster than a regular axis and provides exact integer arithmetic.~  
  
Ok, I think I get it. To fix this, we would indeed have to copy numpy's slow but exact algorithm to sort values into the right bins.

---

## Comment 27

> Username: HDembinski  
> Created at: 2022-08-03 08:33:51 UTC  
> Updated at: 2022-08-03 08:34:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203650935  

> What I can say is that we observe a tiny slowdown in benchmarks with our solution compared to the upstream code, which is surprising since we use integer math in the relevant case. I assume that `boost::rational` would be even slower, but again @HDembinski may know this much better.  
  
I also think that boost::rational would be slower, because one needs to spend more CPU cycles to do the same work. Your solution should be pretty fast since `mIsIntegral` is constant after construction and the branch predictors will take the right branch each time. That being said, predicting the performance of modern CPUs is very difficult even for experts. @emmenlau, why do you override `value_type value(real_index_type aIndex) const noexcept` in your nice solution on Godbolt? Wouldn't the normal implementation work, too?

---

## Comment 28

> Username: emmenlau  
> Created at: 2022-08-03 09:11:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203690557  

Thanks for the thumbs up! I should say that all the nice work was done by @vakokako .  
  
> why do you override value_type value(real_index_type aIndex) const noexcept in your nice solution on Godbolt? Wouldn't the normal implementation work, too?  
  
From a brief look at the code I agree that may not be required. Possibly it's just an artifact from a previous test. I'll check with @vakokako and get back to you today!

---

## Comment 29

> Username: HDembinski  
> Created at: 2022-08-03 10:53:33 UTC  
> Updated at: 2022-08-03 10:54:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203790862  

I did the check, it turns out that the implementation of value() also needs to be modified. The version that I use ensures that the upper edge of the last bin is equal to the upper edge that the user supplied, while the calculation that @vakokako uses only guarantees that for bin widths that are integral values, but for that case it gives correct bin edges, while my version does not.

---

## Comment 30

> Username: emmenlau  
> Created at: 2022-08-03 11:26:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203823039  

Thanks a lot @HDembinski for the quick check, now I recall the same from our discussions!  
  
Let me kindly ask, do you think something like this (our custom axis with implicit integral support) could be included into the official library? We would be more than happy to provide a reasonable PR. Or if you can do something based on our work, please feel free to copy and/or use the code at godbolt to your liking. Consider it to be public domain, or any license that suits your needs.  
  
The axis is nice because at little extra code, and virtually no runtime overhead, it transparently supports the integral (precise) case but also the floating cases. No conscious action from the user required.

---

## Comment 31

> Username: HDembinski  
> Created at: 2022-08-03 11:55:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203852151  

Yes, I plan to finally fix this and I will use your research. I am very grateful for the offer of a PR but I think I need to use this opportunity to also refactor regular which became quite complex with all the optional functionality. Good design is an iterative process which I can do more quickly on my own.

---

## Comment 32

> Username: emmenlau  
> Created at: 2022-08-03 11:55:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203852794  

Thanks a lot for your consideration and help! I'm more than happy about this approach!

---

## Comment 33

> Username: Dominic-Stafford  
> Created at: 2022-08-03 12:35:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1203892120  

Thanks @HDembinski and @emmenlau! It would be really nice to have a solution in the official library since we're using this via the python bindings, but I agree it's worth taking the time to settle on a good solution.

---

## Comment 34

> Username: emmenlau  
> Created at: 2023-02-27 14:40:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1446451859  

Dear @HDembinski, I just wanted to kindly promote this issue and your nice suggestion to implement this in a future version of the library. We would be delighted to have a more "official" support for this feature, which we use a lot.

---

## Comment 35

> Username: HDembinski  
> Created at: 2023-02-27 17:27:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1446743983  

Thanks for pinging me on this. I want to fix this, but I am not sure what is the best way.

---

## Comment 36

> Username: HDembinski  
> Created at: 2023-02-27 17:29:51 UTC  
> Updated at: 2023-02-28 10:51:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1446746972  

The use case you have is covered by `axis::integer<double>`, I think, which should be faster than a regular axis with a special case for bins of integer width. It works when the bin width is exactly 1. It does not support bin widths of larger integer sizes, 2, 3, 4, though.  
  
I cannot just copy your implementation for the issue that you supplied via godbolt, because using an `if` evaluated at runtime in the regular axis for such a thing is going against the rationale. The proper solution in my opinion is to add an option to the regular axis which makes it precise, using the algorithm that Numpy uses, which corrects numerical errors. This is costly compared to the current implementation, however, so this should be a compile-time option.  
  
I started working on that feature a while ago but got distracted by other projects.

---

## Comment 37

> Username: ryanelandt  
> Created at: 2023-06-14 18:51:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/336#issuecomment-1591811401  

I think the precision problems discussed in this issue may be addressed by PR draft #386. I added a [unit test](https://github.com/ryanelandt/histogram/blob/28838d2cf50982ed7546da9e86b77d9d810275b2/test/axis_int_resolver_test.cpp#LL108C1-L150C4) based on @vakokako's example that demonstrates exact `index` and `value` calculations for:  
- integer bin widths (e.g., 1, 2, 3)  
- fractional bin widths (e.g., 0.375, 1.625, 7.25)    
- various offsets (e.g., -0.5, 0.0, 1000.25).  
  
This method is cheap, as it does **not** correct for numerical errors. Could this address some of the precision problems raised in this issue?  
  
Note: this method is only exact when numbers take few floating point bits to represent. For example, it will be exact for a bin width of 1/2 = 0.5, but not for 1/3 = 0.333333333333333...
