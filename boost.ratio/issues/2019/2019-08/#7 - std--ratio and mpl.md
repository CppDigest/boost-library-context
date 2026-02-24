# #7 - std::ratio and mpl [Open]

> Username: Mike-Devel  
> Created at: 2019-08-21 05:08:22 UTC  
> Updated at: 2020-01-28 07:04:48 UTC  
> Url: https://github.com/boostorg/ratio/issues/7  

Is there any possibility/ what would be necessary to provide the same Boost.Mpl integration for std::ratio as for boost::ratio?

---

## Comment 1

> Username: viboes  
> Created at: 2019-08-26 05:21:01 UTC  
> Url: https://github.com/boostorg/ratio/issues/7#issuecomment-524719501  

Could you provide a use case?  
I wanted to extract the dependency to boost.mpl ans boost.rational, but had nver the time.  
  
Maibe you could consider la possibilité of a subibrary to reduce dependencies.

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2019-08-26 08:28:30 UTC  
> Url: https://github.com/boostorg/ratio/issues/7#issuecomment-524770673  

@viboes: I don't need it (I've never used mpl directly).   
It just was something that came up when I tried to make Boost.Chrono and Boost.Ratio an alias for the respective `std::` types/lib. The integration with Boost.Mpl was the one thing where I didn't see a straight way to replicate it for std::ratio, as that is based on boost specific members.
