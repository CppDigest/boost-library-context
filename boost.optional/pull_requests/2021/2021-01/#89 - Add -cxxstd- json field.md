# #89 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-21 04:06:38 UTC  
> Updated at: 2021-01-21 18:50:04 UTC  
> Merged at: 2021-01-21 18:50:04 UTC  
> Closed at: 2021-01-21 18:50:04 UTC  
> Url: https://github.com/boostorg/optional/pull/89  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2021-01-21 18:07:40 UTC  
> Url: https://github.com/boostorg/optional/pull/89#issuecomment-764836653  

I just wonder why it is "03" rather than "98". Is the Boost policy here that "03" means arbitrarily old compiler?

---

## Comment 2

> Username: eldiener  
> Created_at: 2021-01-21 18:31:05 UTC  
> Url: https://github.com/boostorg/optional/pull/89#issuecomment-764850296  

Generally '03' means C++03. I do not think that there is much point to specifying C++98, with '98', but if that is what you want I can change it. Most end-users will, I think, understand that C++03 is more or less the same as C++98, and most end-users could probably not tell what the differences are between C++98 and C++03.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2021-01-21 18:49:54 UTC  
> Url: https://github.com/boostorg/optional/pull/89#issuecomment-764860663  

No, I do not insist on having "98". I was just curious if this has been considered.

---
