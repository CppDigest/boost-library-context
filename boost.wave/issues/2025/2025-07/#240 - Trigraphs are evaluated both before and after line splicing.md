# #240 - Trigraphs are evaluated both before and after line splicing [Open]

> Username: PanoramixDeDruide  
> Created at: 2025-07-25 10:21:06 UTC  
> Updated at: 2025-10-30 23:59:20 UTC  
> Url: https://github.com/boostorg/wave/issues/240  

Just discovered that the Wave Driver (not sure about other setups) preprocesses the following code in a way that doesn't conform to the standard:  
```  
???/  
?=define X 2  
X  
```  
Is evaluated as `2`, while the standard (at least from what I know, would love a source on this) says trigraphs aren't supposed to be evaluated after line splicing happens. Therefore, I believe the correct way to process the above snippet should be to convert `??/` to `\`, see there are no more trigraphs to convert, splice the first two lines into `??=define X 2`, and then not to convert the `??=` trigraph to a `#` because we've already spliced lines. Expected output is as follows:  
```  
??=define X 2  
X  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2025-10-30 23:59:20 UTC  
> Url: https://github.com/boostorg/wave/issues/240#issuecomment-3470746970  

Sorry about the delay! Let me see if I have this right. You expect:  
  
```C  
???/  
?=define X 2  
```  
  
to become via trigraph substitution of `??/`:  
```C  
?\  
?=define X 2  
```  
  
and then, via line splicing:  
```C  
??=define X 2  
```  
  
without any further trigraph substitution occurring? And that Wave behaves differently?  
  
Based on experimenting with clang and gcc, and comparing the output to the `lexed_tokens` sample, I agree. Wave seems to be excessively aggressive, performing at least two (conceptual) passes here.  
  
That said, trigraphs seem fairly obscure - so much so that they were eliminated in C++17. The [gcc docs](https://gcc.gnu.org/onlinedocs/cpp/Initial-processing.html) say:  
  
> Trigraphs are not popular and many compilers implement them incorrectly. Portable code should not rely on trigraphs being either converted or ignored.  
  
As a result, I think this is so low priority as to become `WONTFIX` in Wave. In fact I think you could argue that supporting them past C++14 should count as a bug :)
