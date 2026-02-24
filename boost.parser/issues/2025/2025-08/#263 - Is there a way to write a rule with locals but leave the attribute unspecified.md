# #263 - Is there a way to write a rule with locals but leave the attribute unspecified? [Closed]

> Username: jwwalker  
> Created at: 2025-08-06 18:08:55 UTC  
> Updated at: 2025-08-07 16:38:42 UTC  
> Closed at: 2025-08-07 16:38:42 UTC  
> Url: https://github.com/boostorg/parser/issues/263  

Is there a way to write a rule with locals but leave the attribute unspecified?

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-08-07 11:24:21 UTC  
> Url: https://github.com/boostorg/parser/issues/263#issuecomment-3163667178  

If you have a look into parser_fwd.hpp, you can see the declaration of "rule":  
```  
    template<  
        typename TagType,  
        typename Attribute = no_attribute,  
        typename LocalState = no_local_state,  
        typename ParamsTuple = no_params>  
    struct rule;  
```  
You see that "Attribute" is "no_attribute" if you do not specify it. So you can write  
```  
boost::parser::rule<struct your_tag_type, boost::parser::no_attribute, your_local_state_type> yourrule = "yourruledescription";  
```

---

## Comment 2

> Username: jwwalker  
> Created at: 2025-08-07 16:38:42 UTC  
> Url: https://github.com/boostorg/parser/issues/263#issuecomment-3164964534  

Thanks!
