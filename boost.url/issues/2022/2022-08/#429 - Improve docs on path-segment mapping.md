# #429 - Improve docs on path/segment mapping [Closed]

> Username: alandefreitas  
> Created at: 2022-08-18 21:22:55 UTC  
> Updated at: 2022-10-21 20:13:20 UTC  
> Closed at: 2022-10-21 20:13:20 UTC  
> Url: https://github.com/boostorg/url/issues/429  

The properties:  
  
* Two absolute or relative paths may map to the same sequence.  
* Every sequence determines a unique absolute or relative path.  
  
Example:   
  
- `"./a"` and `"a"` would both map to `{"a"}`  
- `{"a"}` would always map to either `"./a"` or `"a"`, depending on whether the `"."` prefix is necessary to keep the URL valid  
  
Sequences don't iterate the leading "." as this would introduce surprises instead of minimizing them:  
  
- When you assign `{ x, y, z }` to segments, segments may not contain `{ x, y, z }` after that  
  
In other words, the contents of the segment container are authoritative, and the path string is a function of them. Not vice-versa.
