# #138 - Docs: Typo in attribute generation page [Closed]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 18:08:20 UTC  
> Updated at: 2024-03-15 21:29:30 UTC  
> Closed at: 2024-03-15 21:29:30 UTC  
> Url: https://github.com/boostorg/parser/issues/138  

On the page [Attribute Generation](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/attribute_generation.html), the section titled "Another special case: `std::string` assignment", the paragraph after the code block reads:  
> ... you are even more likely to have made a mistake. Though this should work, because the assignment in `std::string s; s = 3.6;` is well-formed [...]  
  
The code example doesn't use `3.6`, it uses `3`.
