# #62 - Cleaner description of attribute combination [Closed]

> Username: akrzemi1  
> Created at: 2024-01-14 09:59:41 UTC  
> Updated at: 2024-01-14 22:18:42 UTC  
> Closed at: 2024-01-14 22:18:42 UTC  
> Url: https://github.com/boostorg/parser/issues/62  

The table in [Combining Operations](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/combining_operations.html) implies that the attribute of `(int_ % ',') | attr(std::vector<int>{})`  would be `std::variant<std::vector<int>, std::vector<int>>`. But it isn't due to special rules. While these special rules are described in [Parser attributes](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/attribute_generation.html#boost_parser__proposed_.tutorial.attribute_generation.parser_attributes), we cannot see them from the Table 1.2. (Combining Operations and Their Semantics)

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-14 20:26:10 UTC  
> Url: https://github.com/boostorg/parser/issues/62#issuecomment-1891063357  

Ah, thanks,  I plain forgot to mention that.
