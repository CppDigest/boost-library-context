# #720 - Modernize Multiprecision's Visualizers in VS [Open]

> Username: ckormanyos  
> Created at: 2025-07-11 17:40:14 UTC  
> Updated at: 2025-07-11 18:33:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/720  

The purpose of this issue is to track attempted modernization of Multiprecision's Visualizers in VS. The visualizer development in MSVC has been modernized by the supplier. It seems to use managed `C#`. It should be feasible to start with `cpp_dec_float` and glue-code for `C#`, although the template parameters might be a bit tricky to work out. If this works out, we can try `cpp_bin_float`.  
  
I can take a try at this one myself.  
  
See also #716  
  
Cc: @jzmaddock and @LegalizeAdulthood
