# #85 - Feature: `constexpr` default constructor [Open]

> Username: burnpanck  
> Created at: 2024-03-15 16:14:13 UTC  
> Updated at: 2024-03-15 16:14:13 UTC  
> Url: https://github.com/boostorg/intrusive/issues/85  

Although in [an older issue](https://github.com/boostorg/intrusive/issues/47), it was concluded that general `constexpr` may be difficult to achieve, I believe there would be considerable benefit in just a `constexpr` default constructor for the container types and the hooks. Since C++20, we have the `constinit` specifier for global variables, which means the global can be initialised without running code, which is desirable especially in bare-metal/kernel scenarios, where there is little one can rely on during early boot. However, `constinit` requires a `constexpr` constructor, for obvious reasons. Global containers in bare-metal scenarios usually start-off empty, so a default constructor that is `constexpr` would already go a long way.
