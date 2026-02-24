# #95 - axis::circular and NaN values [Closed]

> Username: HDembinski  
> Created at: 2018-09-21 08:54:07 UTC  
> Updated at: 2018-10-27 17:47:58 UTC  
> Closed at: 2018-10-27 17:47:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/95  

Currently, axis::circular does not handle NaN values.  
  
There are two solutions with different trade-offs.  
  
1) Allow an optional overflow bin to count NaNs (off by default)  
2) Fail an assert when NaN is used an input  
  
I  prefer option 1.

---

## Comment 1

> Username: jpivarski  
> Created at: 2018-09-21 12:13:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-423510917  

Agreed— linear axes put NaN in the overflow bin and for circular axes, NaN would be the only thing to go in the overflow bin.  
  
Option 2 is bad: it's way too easy to get NaNs for reasons that should not stop a running calculation. For instance, sqrt(big number – big number) that is formally always positive but roundoff error could make it push it below zero. Those are cases where you want to note their existence in an overflow bin, but not stop the calculation.  
  
I guess that wouldn't happen in a circular bin. How about argsin(something close to 1), then.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-09-24 09:41:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-423922379  

I actually changed my mind slightly. There should be an optional overflow bin and it should be on by default. The defaults should give safe operation higher priority than performance.

---

## Comment 3

> Username: ahhz  
> Created at: 2018-10-03 09:45:43 UTC  
> Updated at: 2018-10-03 09:47:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-426574942  

I think there is a third option: Make the NaN bin optional (as a policy, and by default turned off). When turned off, then fail/assert on NaN; when turned on, then count NaN values into special bin. I would not like NaN numbers to be silently discarded (which is my interpretation of option 1 + default). If I had the possibility of missing or invalid data, I would filter these out before passing to histogram, or use my own axis for a value type that reflects this possibility, e.g. ```std::optional<double>```.

---

## Comment 4

> Username: jpivarski  
> Created at: 2018-10-03 11:28:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-426602275  

Actually, we had discussed this option in private. In my own projects, I call the three possibilities overflow, underflow, and nanflow. For simple interpretation, I don't allow NaN values to go to the overflow bin, and the user can choose to include or exclude any of these. However, that makes the implementation more complex, especially when static polymorphism is involved. We can air the idea publicly, but when we last talked, Hans didn't prefer it.

---

## Comment 5

> Username: ahhz  
> Created at: 2018-10-03 11:54:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-426609000  

To be honest, for me it is a bit academic because in my use-cases I would avoid using these special bins when using Histogram, i.e. make sure that my first and last bin are sufficiently wide and handling invalid input upstream. Silenty discarding NaN input just feels wrong to me.

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-10-03 20:49:19 UTC  
> Updated at: 2018-10-03 20:57:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-426797183  

> make sure that my first and last bin are sufficiently wide and handling invalid input upstream  
  
If you care about performance: a regular axis is much faster than a variable axis. The overflow and underflow bins allow you to use a regular axis and still catch the outliers.  
  
The over-/underflow bins are really required, if you want multi-dimensional histograms to behave consistently/unsurprisingly, as explained in the Rationale of the docs.  
  
A nanflow bin, however, is not required. NaNs are the product of an error in the program logic, they should not happen. The library should not consume an extra memory cell per axis for something that should not occur in an error-free program. For me, the two options are either to assert on NaN-free input, or count NaNs in one of the *flow bins. The latter seemed more user-friendly, and therefore NaNs are put into the overflow bin if it is present.

---

## Comment 7

> Username: jpivarski  
> Created at: 2018-10-03 21:02:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-426801760  

Even if you have variable bins in which the first starts at (floating point) minus infinity and the last ends at plus infinity, values of plus infinity won't be included in the last bin because bins are exclusive on the high edge. Performance aside, overflow bins are the only way to include plus infinity in any bin.

---

## Comment 8

> Username: ahhz  
> Created at: 2018-10-03 22:09:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/95#issuecomment-426820573  

Sure, I was not trying to say that you should not provide under/overflow bins, just that I wouldn't use them.
