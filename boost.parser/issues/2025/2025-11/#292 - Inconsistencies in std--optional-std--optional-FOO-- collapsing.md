# #292 - Inconsistencies in std::optional<std::optional<FOO>> collapsing [Open]

> Username: andreasbuhr  
> Created at: 2025-11-15 09:23:51 UTC  
> Updated at: 2025-11-15 09:23:51 UTC  
> Url: https://github.com/boostorg/parser/issues/292  

Hi,  
  
right now I have little time, so I write this short report. I hope to find time in the future to extend it with a reproducer and maybe a proposed fix.  
  
There seem to be some inconsistencies, or at least a lack of documentation, around the collapsing of nested optionals.  
  
Given for example this parser:  
```  
constexpr auto my_parser = bp::lit(U"hallo") >> -(bp::lit(u"there") >> -(bp::lit(U"Markus") >> bp::attr(true)));  
```  
  
Without any collapsing, the argument type would be `std::optional<std::optional<bool>>`. But because of the collapsing, it is a `bp::optional<bool>`. But now I am missing the documentation whether the state of that optional (set or not set) reflects the success of the outer optional parser or the inner optional parser.  
  
Parsing the string "hallo there", it seems I get an optional which is set (set by the outer optional parser). But its content is default-constructed, because the inner optional parser failed and produced an `std::nullopt`;  
  
My feeling is that it would be more consistent if the state of the resulting std::optional reflected the success of the inner optional parser. Either way, the behavior should be documented and tested.
