# #347 Add convolve function to numeric extension [Merged]

> Username: lpranam  
> Created at: 2019-07-22 01:30:59 UTC  
> Updated at: 2019-10-23 21:27:44 UTC  
> Merged at: 2019-07-22 09:20:58 UTC  
> Closed at: 2019-07-22 09:20:58 UTC  
> Url: https://github.com/boostorg/gil/pull/347  

### Description  
This function combines both `convolve_rows` and `convolve_cols` into a single call for user convenience.  
  
The need for such function came into the sight when @miralshah365 was implementing `adaptive_threshold` and encountered error due to multiple calls to convolve function with the same source instead of using the result of the first convolution as the source for other.   
  
### References  
  
- #341   
  
### Tasklist  
  
- [x] Add test case(s) (in #349)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2019-07-22 07:31:54 UTC  
> Url: https://github.com/boostorg/gil/pull/347#issuecomment-513675266  

I believe the names are a bit misleading. My first impression was that `kernel` is allowed to be 2D, but user would have to wait for compilation error or figure out the logic by some other way. Some combination of words "pipe", "2dir" or something like that would be helpful.  
It might be me being a bit picky though, looks good otherwise.

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2019-07-22 09:19:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/347#pullrequestreview-264691810  

Thanks for this utility.  
  
Formally, it should have been rejected that due to missing tests :-)  
but I will add those myself as I'm adding other tests for the convolution implementation

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 175 |+ BOOST_FORCEINLINE
 176 |+ void convolve(const SrcView& src, const Kernel& ker, const DstView& dst,
 177 |+                    convolve_boundary_option option=convolve_option_extend_zero) {
```

> Username: mloskot  
> Created_at: 2019-07-22 09:18:13 UTC  
> Updated_at: 2019-07-22 09:22:14 UTC  
> Url: https://github.com/boostorg/gil/pull/347#discussion_r305747196  

Nitpicking:  
  
```  
) {  
```  
is preferred to be  
```  
)  
{  
```  
  
Such big indentations are not necessary:  
  
```  
                   convolve_...  
```  
  
Finally, I'd call for new code additions to follow the [consistent const](http://slashslash.info/petition/) (aka [East const](https://mariusbancila.ro/blog/2018/11/23/join-the-east-const-revolution/)) :-)


---

## Comment 3

> Username: mloskot  
> Created_at: 2019-07-22 09:21:54 UTC  
> Updated_at: 2019-07-22 09:36:30 UTC  
> Url: https://github.com/boostorg/gil/pull/347#issuecomment-513712949  

@simmplecoder   
> I believe the names are a bit misleading.   
  
What names?  
  
> My first impression was that kernel is allowed to be 2D,   
  
A `KernelConcept` would have been helpful indeed (update: #348).

---

## Comment 4

> Username: simmplecoder  
> Created_at: 2019-07-22 09:25:06 UTC  
> Url: https://github.com/boostorg/gil/pull/347#issuecomment-513714085  

@mloskot   
I mean function and kernel names. May be being a bit more specific would help, but I cannot think of a succinct enough name

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-07-22 09:31:39 UTC  
> Url: https://github.com/boostorg/gil/pull/347#issuecomment-513716388  

@simmplecoder Right. TBH, I did not find the function names too confusing myself, but I did find the `kernel_2d{_fixed}` not well documented or intuitive. Well, that's how original authors from Adobe defined it. It's our job to improve it :)

---

## Review 6 [Commented]

> Username: mloskot  
> Created_at: 2019-07-22 10:22:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/347#pullrequestreview-264724508  

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 177 |+                    convolve_boundary_option option=convolve_option_extend_zero) {
 178 |+     convolve_rows<PixelAccum>(src, ker, dst, option);
 179 |+     convolve_cols<PixelAccum>(dst, ker, dst, option);
```

> Username: mloskot  
> Created_at: 2019-07-22 10:22:58 UTC  
> Updated_at: 2019-07-22 10:26:47 UTC  
> Url: https://github.com/boostorg/gil/pull/347#discussion_r305773220  

@lpranam  This source and destination views overlap in the second call makes me suspicious.  
I always tend to use that with intermediate buffer.  
On the other hand, the `convolve_rows/cols` implementation does the buffering.


---
