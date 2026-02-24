# #236 - [FR] Trivial and noexcept iterators [Open]

> Username: psiha  
> Created at: 2024-02-27 12:57:27 UTC  
> Updated at: 2024-02-27 12:57:27 UTC  
> Url: https://github.com/boostorg/unordered/issues/236  

Please refactor and decorate iterator types/class templates and their key functions (e.g. copy constructors) with appropriate attributes ( [[clang::trivial_abi]], noexcept, constexpr) so they are recognized at the language level (rather than just by the optimizer) as trivial or atleast noexcept (copyable etc.) - so that, for example, I can hide/'pimpl' them behind something like https://github.com/microsoft/proxy and have it use 'trivial' (memcpy) code for copy construction and assignment (instead of going through vtable pointers).
