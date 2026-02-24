# #227 - Finalize benchmarks [Closed]

> Username: vinniefalco  
> Created at: 2020-08-28 20:56:16 UTC  
> Updated at: 2020-08-30 23:39:10 UTC  
> Closed at: 2020-08-30 23:39:10 UTC  
> Url: https://github.com/boostorg/json/issues/227  

1. finalize our choices for the files we use to benchmark  
2. decide which configurations we want to use (e.g. clang, gcc, msvc)  
3. decide on the reference hardware  
4. run the benchmarks  
5. update the images and the benchmarks qbk file to note the hardware and compiler versions

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-29 18:05:24 UTC  
> Updated at: 2020-08-29 18:09:21 UTC  
> Url: https://github.com/boostorg/json/issues/227#issuecomment-683323813  

### Benchmark files  
  
These are the files simdjson uses for the benchmarks in their README.MD. They are mostly non-artificial, so they should be pretty good at illustrating performance for real-world use-cases.   
  
```  
apache_builds.json  
canada.json  
citm_catalog.json  
github_events.json  
gsoc-2018.json  
instruments.json  
marine_ik.json  
mesh.json  
mesh.pretty.json  
numbers.json  
random.json  
twitter.json  
twitterescaped.json  
update-center.json  
```  
  
### Compilers  
  
- clang (trunk) with -O3 and -std=c++17  
- gcc (trunk) with -O3 and -std=c++17  
- msvc 19.28 with /O2 and /std:c++17  
  
### Library Configuration  
  
The default configuration should be used for all benchmarked libraries.  
  
Reasoning: UTF-8 validation causes up to a 50% reduction in speed for RapidJSON, so it will be kept off for the benchmarks. We only pay for UTF-8 validation when UTF-8 characters are encountered, so the performance impact is much less significant.  
  
### Hardware  
  
Ideally we want to provide benchmarks for AMD as well as Intel, but if we aren't able to use both then we should stick with Intel. Only x86-64 will be shown for now; x86-32 and ARM benchmark results can be added later.  
  
As for the actual hardware, the best choice would be to use top of the line consumer chips. Note that for Intel, we should opt for an Ice Lake chip that isn't affected by the [JCC Erratum](https://www.intel.com/content/dam/support/us/en/documents/processors/mitigations-jump-conditional-code-erratum.pdf). If this isn't possible, then the flag `-mbranches-within-32B-boundaries` should be used on clang and gcc.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-29 18:09:14 UTC  
> Url: https://github.com/boostorg/json/issues/227#issuecomment-683324287  

Change Optimally to Ideally

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-29 18:10:26 UTC  
> Url: https://github.com/boostorg/json/issues/227#issuecomment-683324449  

"The default configuration should be used for all benchmarked libraries." will be changed to "The default configuration is used for all benchmarked libraries:" in the docs, followed by a bulleted list describing what each benchmarked library's default configuration actually does
