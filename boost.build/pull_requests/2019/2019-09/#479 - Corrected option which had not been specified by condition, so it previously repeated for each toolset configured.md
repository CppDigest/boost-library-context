# #479 Corrected option which had not been specified by condition, so it previously repeated for each toolset configured [Merged]

> Username: eldiener  
> Created at: 2019-09-15 01:56:52 UTC  
> Updated at: 2021-10-02 21:13:51 UTC  
> Merged at: 2019-09-19 16:55:31 UTC  
> Closed at: 2019-09-19 16:55:31 UTC  
> Url: https://github.com/boostorg/build/pull/479  

By not specifying the 'condition' when adding the option, the option repeated itself on the command line for each intel-win toolset configured. Luckily this did not create an error during the compile, but of course was wrong in the intel-win configuration code. This fix corrects that repetition.

---
