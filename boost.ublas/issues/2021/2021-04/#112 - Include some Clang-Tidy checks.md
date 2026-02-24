# #112 - Include some Clang-Tidy checks [Open]

> Username: bassoy  
> Created at: 2021-04-29 07:42:41 UTC  
> Updated at: 2021-05-01 14:42:28 UTC  
> Url: https://github.com/boostorg/ublas/issues/112  

The configuration [file](https://github.com/boostorg/ublas/blob/develop/.clang-tidy) excludes clang-tidy checks  
```bash  
google-readability-braces-around-statements  
google-explicit-constructor  
hicpp-vararg  
cppcoreguidelines-pro-type-vararg  
```  
We should include them.  
- `google-readability-braces-around-statements` requires simple refactoring  
- `google-explicit-constructor` needs significant rework also in the test files and results in API change  
- `hicpp-vararg` needs usage of the boost lightweight unit test instead of the boost-test
