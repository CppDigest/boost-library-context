# #883 - Explore test_suite.hpp command line arguments [Closed]

> Username: alandefreitas  
> Created at: 2024-11-11 19:05:08 UTC  
> Updated at: 2025-06-19 00:49:28 UTC  
> Closed at: 2025-06-19 00:49:28 UTC  
> Url: https://github.com/boostorg/url/issues/883  

The inability to pass command line arguments through test_suite.hpp is a problem.  
  
We need to change the tool to support the existing features and also allow passing params.  
  
https://github.com/boostorg/url/blob/4980f8ec37984a0928d9f6324d5d6b57b58838d7/extra/test_main.cpp#L473  
  
Currently, every command line argument is interpreted as the name of a suite to run. As if all params are values for the "inputs" parameter.  
  
Although including more options is relatively easy, the problem you want to solve is a little more complex. Because we want to pass information to the tests rather than the test suite. It's contextual. So we need to agree on a convention to forward these parameters to the test suite.  
  
We should make a short list of choices, and then decide. Some options are:  
  
- User overrides main and works on the command line arguments to store important values in some global variables before calling `return ::test_suite::detail::run(log, argc, argv);`  
- `test -1 path/to/file.txt` puts the string in some global array at position 1

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-11-11 19:05:41 UTC  
> Url: https://github.com/boostorg/url/issues/883#issuecomment-2468850016  

@anarthal any ideas?

---

## Comment 2

> Username: anarthal  
> Created at: 2024-11-11 20:53:53 UTC  
> Url: https://github.com/boostorg/url/issues/883#issuecomment-2469003860  

Boost.Test (and other tooks like git) do this by having a separator argument `--`. Anything before the separator is parsed by the framework, anything after it, by the user.
