# #45 - Error handing of JSON parsing not expresive [Open]

> Username: JVApen  
> Created at: 2019-11-19 17:05:23 UTC  
> Updated at: 2019-11-20 17:19:02 UTC  
> Url: https://github.com/boostorg/property_tree/issues/45  

The error handling of parsing JSON is not expressive enough in order to solve related problems.  
  
Looking at https://www.boost.org/doc/libs/1_59_0/boost/property_tree/detail/json_parser/parser.hpp  
void parse_escape(), we fail the function with parse_error("invalid escape sequence");  
  
Unfortunately, if all your JSON is on a single line, this tells you that there is a problem on line 1.  
To make this message more usable, it would make sense to not only store the line number, though also the column number, so we can check which character is incorrectly escaped. In an ideal world, the exception would even contain that character.

---

## Comment 1

> Username: JVApen  
> Created at: 2019-11-20 17:19:01 UTC  
> Url: https://github.com/boostorg/property_tree/issues/45#issuecomment-556127430  

This remark actually holds for all of the parse errors, as we also encountered already the same error with following message: expected value
