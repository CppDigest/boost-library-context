# #1123 - Show intrusive conversions for `ip_address` in documentation [Open]

> Username: pdimov  
> Created at: 2025-12-01 17:07:12 UTC  
> Updated at: 2025-12-01 17:07:12 UTC  
> Url: https://github.com/boostorg/json/issues/1123  

Currently, the conversions for `ip_address` in the documentation are written to only use its public interface; this presumably demonstrates how conversions can be implemented non-intrusively without modifying the original (third-party) type.  
  
However, in this case the same serialized result can be achieved much more simply if the `tag_invoke` overloads are made inline friends, because they can just de/serialize the `octets_` member directly.  
  
(This will result in significant lines of code savings for the non-throwing `value_to`.)  
  
This will be a good opportunity to also recommend the use of inline friends over functions in the enclosing namespace, because they don't contribute to the overload set unless needed, which speeds up overload resolution and shortens compile times. (This is especially important for `tag_invoke`, because the function name is the same so the namespace can quickly fill up with overloads.)
