# #204 - symbols not parsing as subparser rules [Closed]

> Username: vmauery  
> Created at: 2024-10-30 17:45:04 UTC  
> Updated at: 2024-11-01 04:08:20 UTC  
> Closed at: 2024-11-01 04:08:20 UTC  
> Url: https://github.com/boostorg/parser/issues/204  

I recently updated to the latest boost::parser and found, to my delight, that there was now a way to insert/delete/clear symbol tables for the next parse. So I made changes in my code to use this. But it wasn't parsing ANY of the symbols. Other bits from the parser were working, just not the symbols. I made a simplified use case to show what I am doing, but basically, if I understand the issue correctly, symbols parse fine as the top-level parser, but do not parse as a subparser. And I think this is the case whether or not they are mutated.  
  
I have attached my use case.[symbol_parser.cpp.txt](https://github.com/user-attachments/files/17576910/symbol_parser.cpp.txt)

---

## Comment 1

> Username: vmauery  
> Created at: 2024-10-30 20:28:43 UTC  
> Url: https://github.com/boostorg/parser/issues/204#issuecomment-2448308202  

Looking more into it, the top-level symbol parser always works, but the sublevel symbol parser only works if the symbol table is loaded as part of its constructor. Added items after the constructor fail to get parsed; while items added as part of the constructor parse successfully. In fact, if I initially add items in the constructor, then clear_for_next_parse/insert_for_next_parse, then parse again, only the original items are parsed successfully. So it looks like it has something to do with failing to process the clear_for_next_parse/insert_for_next_parse operations in the parser. But this only happens for the sublevel parsing. For top-level parsing, the clear_for_next_parse/insert_for_next_parse works fine

---

## Comment 2

> Username: vmauery  
> Created at: 2024-10-30 23:45:48 UTC  
> Url: https://github.com/boostorg/parser/issues/204#issuecomment-2448684131  

This is not the real fix, but this fixes it for me.  
  
```diff  
diff --git a/include/boost/parser/parser.hpp b/include/boost/parser/parser.hpp  
index 33d20103..97840b40 100644  
--- a/include/boost/parser/parser.hpp  
+++ b/include/boost/parser/parser.hpp  
@@ -1676,7 +1676,7 @@ namespace boost { namespace parser {  
                 any = trie_t{};  
                 has_case_folded = false;  
                 trie_t & trie = *std::any_cast<trie_t>(&any);  
-                if (pending_ops) {  
+                if (sym_parser.pending_operations().size() > 0) {  
                     detail::apply_symbol_table_operations(  
                         sym_parser.initial_elements(),  
                         sym_parser.pending_operations());  
  
```

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-10-31 05:56:22 UTC  
> Updated at: 2024-10-31 05:57:06 UTC  
> Url: https://github.com/boostorg/parser/issues/204#issuecomment-2449057985  

Thanks for reporting this!  I'm able to reproduce this locally.  It does indeed seem to be failing because the symbol table is used inside a rule.  Working on a fix now.  
  
Also, thanks for the very simple test case.
