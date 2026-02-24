# #1215 - Review 2: issignaling signature [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 19:32:36 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-14 13:31:13 UTC  
> Url: https://github.com/boostorg/decimal/issues/1215  

>There is a thing here that we may not be able to reproduce but it happens with  
the built-in binary floating point types that the hardware itself handles.  
"Looking at"/reading a signaling NaN potentially raises the signal and  
then converts it into a silent NaN.  AFAIK that includes copying it, but I may  
remember wrong.  If I am correct (which I may not be) an `issignaling` function  
that takes its argument by copy would always return `false` as the reading for  
copying would turn the source of the actual parameter into a silent NaN (after  
raising a signal if requested) and then copy that silent NaN into the  
parameter.  I may be wrong about that, a test program should be written to  
verify how this works with `double`.  
  
>The issue here is that if a CPU or a compiler ever implements decimal floating  
point and we would like to update this library to use the native implementation  
of these types the `issignaling` function may stop working due to its signature.  
  
Am I correct in thinking that this function is basically future-proofing the library since we don't actually have any signalling NaNs?  
  
The same argument would apply to `read_payload` as well.  
  
I note that all the std lib functions that accept a NaN take it by value: the implication being that they will signal if used with a signalling NaN.  Maybe `read_payload` should be the same, and `issignalling` has no real use - at least not yet?

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 08:17:20 UTC  
> Url: https://github.com/boostorg/decimal/issues/1215#issuecomment-3495776624  

The only real uses of `issignaling` is for formatting of those kinds of NANs, and IEEE's `totalcompare` function which defines the ordering of NANs.

---

## Comment 2

> Username: mborland  
> Created at: 2025-11-14 09:01:55 UTC  
> Url: https://github.com/boostorg/decimal/issues/1215#issuecomment-3531683057  

For posterity in IEEE 754 Section 5.7.2:  
  
Implementations shall provide the following non-computational operations for all supported arithmetic  
formats and should provide them for all supported interchange formats. They are never exceptional, even  
for signaling NaNs.  
  
The listed functions are the normal fpclassify type functions as well as a specific `issignaling`. I have added a reference to this in the docs.
