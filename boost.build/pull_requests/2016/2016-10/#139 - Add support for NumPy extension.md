# #139 Add support for NumPy extension. [Merged]

> Username: stefanseefeld  
> Created at: 2016-10-14 01:22:38 UTC  
> Updated at: 2021-10-02 22:19:06 UTC  
> Merged at: 2016-10-14 01:43:33 UTC  
> Closed at: 2016-10-14 01:43:33 UTC  
> Url: https://github.com/boostorg/build/pull/139  

This is an attempt to add NumPy support to Boost.Build, meaning:  
- The presence of NumPy in the configured Python version is detected automatically.  
- The presence is presented as the 'python.numpy' rule, so Boost.Python can add conditional build logic.  
- A new 'numpy-test' rule is defined that runs NumPy extension tests.  
  
I seem to be able to build (and test) Boost.Python both in the presence and absence of NumPy. As the associated Boost.Python build infrastructure changes depend on this, I'd much appreciate someone reviewing this PR soon. (Ideally I'd like to merge the full NumPy support into Boost in time for the 1.63 release.)  
Many thanks,

---
