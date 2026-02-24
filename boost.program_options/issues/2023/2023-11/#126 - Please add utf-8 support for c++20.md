# #126 - Please add utf-8 support for c++20 [Open]

> Username: pavel-machyniak  
> Created at: 2023-11-10 11:11:50 UTC  
> Updated at: 2023-11-10 11:23:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/126  

It would be nice if the library would accept `std::u8string` and `char8_t` types when compiling for **c++20** or higher, allowing direct interaction with **utf-8** encoded unicode strings.

---

## Comment 1

> Username: vprus  
> Created at: 2023-11-10 11:23:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/126#issuecomment-1805547125  

Thanks for your proposal. It probably would be nice indeed, but I'm afraid it would require someone to create a PR, since I don't have time for any feature development.
