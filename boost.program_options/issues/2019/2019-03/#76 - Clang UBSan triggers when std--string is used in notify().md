# #76 - [Regression 1.69.0] Clang UBSan triggers when std::string is used in notify() [Open]

> Username: nafur  
> Created at: 2019-03-11 14:10:49 UTC  
> Updated at: 2019-03-11 14:10:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/76  

We found that clang UBSan shows undefined behaviour when we use an option of type std::string with po::notify(). Please see https://stackoverflow.com/q/55064334/2375725 for more details and a minimal working example.  
My colleague has already bisected the issue in [this comment](https://stackoverflow.com/questions/55064334/ubsan-boostprogram-options-with-stdstring?noredirect=1#comment96884751_55064334) and it seems to be related to activating ``-fvisibility-inlines-hidden`` and ``-fvisibility=hidden``.  
Unfortunately we have no idea where to go from here...  
  
We will also open a ticket at clang as we are really not sure whether this is a false positive in clang UBSan or actual undefined behaviour.
