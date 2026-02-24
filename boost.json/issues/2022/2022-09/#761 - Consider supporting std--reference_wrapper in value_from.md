# #761 - Consider supporting std::reference_wrapper in value_from [Open]

> Username: grisumbras  
> Created at: 2022-09-29 08:43:51 UTC  
> Updated at: 2022-09-29 08:43:51 UTC  
> Url: https://github.com/boostorg/json/issues/761  

A `tag_invoke` overload should be enough, but alternatively a whole category of proxy types can be created with opt-in trait `is_proxy_type`.
