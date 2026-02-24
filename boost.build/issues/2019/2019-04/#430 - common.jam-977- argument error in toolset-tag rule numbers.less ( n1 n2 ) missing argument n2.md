# #430 - [1.70] common.jam:977: argument error in toolset-tag rule numbers.less ( n1 n2 ) missing argument n2 [Closed]

> Username: jschueller  
> Created at: 2019-04-30 11:33:37 UTC  
> Updated at: 2019-04-30 13:14:05 UTC  
> Closed at: 2019-04-30 13:14:04 UTC  
> Url: https://github.com/boostorg/build/issues/430  

@pdimov the new compiler version detection in 1.70 (https://github.com/boostorg/build/commit/9df3a65a2842090641ca0dedd36bc016f790fbeb)  
fails when the compiler version is not defined

---

## Comment 1

> Username: jschueller  
> Created at: 2019-04-30 13:14:04 UTC  
> Url: https://github.com/boostorg/build/issues/430#issuecomment-487947006  

we provided a version string with "custom" in it, so it's not really a bug I guess
