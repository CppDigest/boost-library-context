# #247 - CI testing needs to be updated [Closed]

> Username: eldiener  
> Created at: 2020-05-21 07:34:16 UTC  
> Updated at: 2022-09-18 14:45:29 UTC  
> Closed at: 2022-09-18 14:45:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/247  

The CI tests have failures in clang implementations which are becausde of the versions of clang being used. Please upgrade versions of clang to be tested in the CI tests. Currently some CI tests will always fail no matter what the PR entails.
