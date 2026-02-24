# #15 - allow tokenizing string_view to string_view(s) [Open]

> Username: cedral  
> Created at: 2019-12-17 00:44:35 UTC  
> Updated at: 2020-08-15 16:02:43 UTC  
> Url: https://github.com/boostorg/tokenizer/issues/15  

This requires getting around the dependency on std::string's assign function. I added the following specialization to assign_or_plus_equal to get it working but it could probably be done better.   
```  
    template<class Iterator, typename char_t>  
    static void assign(Iterator b, Iterator e, std::basic_string_view<char_t>& t)  
    {  
        t = { std::addressof(*b), static_cast<size_t>(std::distance(b, e)) };  
    }  
```

---

## Comment 1

> Username: poyenc  
> Created at: 2020-08-15 16:02:43 UTC  
> Url: https://github.com/boostorg/tokenizer/issues/15#issuecomment-674416721  

Cool! I think it's good point to start supporting `basic_string_view`
