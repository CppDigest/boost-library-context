# #113 - detail identifiers in public APIs could say __implementation_defined__ [Open]

> Username: vinniefalco  
> Created at: 2021-11-02 00:06:39 UTC  
> Updated at: 2022-01-24 16:32:34 UTC  
> Url: https://github.com/boostorg/docca/issues/113  

For example here  
```  
template<class CharSet>  
detail::pct_encoded_bnf<CharSet>  
pct_encoded_bnf(  
    CharSet const& cs,  
    pct_encoded_str& t) noexcept;  
```  
  
Currently the workaround is to write  
```  
template<class CharSet>  
#ifdef BOOST_URL_DOCS  
__implementation_defined__  
#else  
detail::pct_encoded_bnf<CharSet>  
#endif  
pct_encoded_bnf(  
    CharSet const& cs,  
    pct_encoded_str& t) noexcept;  
```

---

## Comment 1

> Username: evanlenz  
> Created at: 2022-01-24 07:14:57 UTC  
> Url: https://github.com/boostorg/docca/issues/113#issuecomment-1019787406  

Are there any existing examples of this workaround in the URL library? (I'm not finding any.) That's fine if not, but if we do have existing examples, they would serve as good test cases for me.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-01-24 16:32:34 UTC  
> Url: https://github.com/boostorg/docca/issues/113#issuecomment-1020291178  

I didn't like public functions returning types that were private so I refactored Boost.URL to not do this. We can put a hold on this issue until it comes up again :)
