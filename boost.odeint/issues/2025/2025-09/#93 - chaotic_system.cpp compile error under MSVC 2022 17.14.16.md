# #93 - chaotic_system.cpp compile error under MSVC 2022 17.14.16 [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 18:24:54 UTC  
> Updated at: 2025-12-02 10:08:19 UTC  
> Closed at: 2025-12-02 10:08:19 UTC  
> Url: https://github.com/boostorg/odeint/issues/93  

The example code assumes that `x.begin()` is implicitly convertible to `const double *`, but instead `x.begin()` returns a `const_iterator` type that can't be implicitly (or explicitly) converted to a pointer.  Using `x.data()` instead of `x.begin()` corrects the problem.
