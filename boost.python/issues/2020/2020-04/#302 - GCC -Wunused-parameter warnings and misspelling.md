# #302 - GCC -Wunused-parameter warnings and misspelling [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-11 18:15:19 UTC  
> Updated at: 2020-04-11 18:15:19 UTC  
> Url: https://github.com/boostorg/python/issues/302  

I built Boost 1.72 with GCC 7.5 with added -Wextra in cxxflags. Problems are still in current code:  
  
libs/python/src/object/function_doc_signature.cpp:108:118: warning: unused parameter ‘cpp_types’ [-Wunused-parameter]  
libs/python/src/object/function_doc_signature.cpp:108:99: warning: unused parameter ‘n_overloads’ [-Wunused-parameter]  
  
Misspelling: libs/python/src/object/function_doc_signature.cpp:37: shold
