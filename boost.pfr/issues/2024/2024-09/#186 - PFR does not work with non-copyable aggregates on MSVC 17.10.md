# #186 - PFR does not work with non-copyable aggregates on MSVC 17.10 [Open]

> Username: Kaiyakha  
> Created at: 2024-09-26 08:42:53 UTC  
> Updated at: 2024-11-08 14:25:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/186  

I was surprised to find out PFR fails to compile the following non-copyable aggregate `Entity` with MSVC 17.10:  
```  
struct Field final {  
  Field() = default;  
  Field(const Field&) = delete;  
};  
  
struct Entity final {  
  Field x;  
};  
  
Entity e;  
  
boost::pfr::get<0>(e);  
```  
The output says the tuple index is out of bounds. I also tried `pfr::for_each_field` which resulted in that scary _If there is no other failed static asserts then something went wrong_. My colleagues use Clang and they say the code above works just fine. The latest GCC also didn't show any issues.

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-10-14 06:16:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/186#issuecomment-2410113014  

I'm afraid this is a MSVC issue. Looks like guaranteed copy elision is not supported by the compiler.  
  
Anyway, I have no access to that compiler. If there's a known workaround, I'd appreciate a PR.

---

## Comment 2

> Username: denzor200  
> Created at: 2024-11-08 14:25:46 UTC  
> Url: https://github.com/boostorg/pfr/issues/186#issuecomment-2464896428  

Have you tried `-DBOOST_PFR_HAS_GUARANTEED_COPY_ELISION=0` ?
