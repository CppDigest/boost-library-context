# #1 build target for preprocessing Fusion with wave [Merged]

> Username: ericniebler  
> Created at: 2014-01-21 22:50:21 UTC  
> Updated at: 2014-08-02 15:17:30 UTC  
> Merged at: 2014-01-21 23:12:28 UTC  
> Closed at: 2014-01-21 23:12:28 UTC  
> Url: https://github.com/boostorg/fusion/pull/1  

This change adds a preprocess build target. It also makes changes that are necessary to preprocess successfully with wave. Tested with:  
- msvc-11,  
- msvc-12,  
- gcc-4.8 (with and without std-gnu++11),   
- clang-3.3 (with and without std-gnu++11), and  
- clang-trunk (with and without std-gnu++11).   
  
I also ran tests using clang-trunk (with and without std-gnu++11) with BOOST_FUSION_DONT_USE_PREPROCESSED_FILES. In all cases, the results appear at a cursory glace to be identical to what's currently being reported on Boost's regression report.  
  
The large number of diffs are because the preprocessed headers apparently haven't been regenerated in a while, and were out of sync with the rest of Fusion. Also, tests for C++11 features were being stripped during wave pre-preprocessing.

---
