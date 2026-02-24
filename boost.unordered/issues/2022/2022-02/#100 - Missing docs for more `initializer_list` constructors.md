# #100 - Missing docs for more `initializer_list` constructors [Closed]

> Username: cmazakas  
> Created at: 2022-02-14 22:52:31 UTC  
> Updated at: 2022-03-28 21:26:15 UTC  
> Closed at: 2022-03-28 21:26:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/100  

Docs in the reference appear to be missing for:  
```cpp  
    unordered_map(initializer_list<value_type> il, size_type n, const allocator_type& a);  
    unordered_map(initializer_list<value_type> il, size_type n, const hasher& hf,  
                  const allocator_type& a)  
```  
  
These weren't part of the original documentation and will require new text.  
  
The rest of the containers will need to be audited as well.
