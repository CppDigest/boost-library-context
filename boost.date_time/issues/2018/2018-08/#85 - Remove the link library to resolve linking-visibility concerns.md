# #85 - Remove the link library to resolve linking/visibility concerns [Closed]

> Username: jeking3  
> Created at: 2018-08-19 14:30:40 UTC  
> Updated at: 2020-04-05 17:58:57 UTC  
> Closed at: 2020-04-05 17:58:56 UTC  
> Url: https://github.com/boostorg/date_time/issues/85  

The link library can cause problems, for example:  
  
https://github.com/boostorg/date_time/pull/34  
also the mailing list topic of "[boost] [all] Request for out of the box visibility support".  
  
By removing the link library entirely this issue goes away.  In my discussion with Jeff Garland, the original author:  
  
> If you look carefully at what is going on in the .cpp files it’s basically for standard string conversion support for things like months.  So greg_month supports things like to_long_string() which need to take a ‘1’ and convert it into ‘January’.  Anyway, there’s a few places in the library that this is used which would have to be replaced by a header compatible implementation.   
>   
> For example see line 67 here.  
> https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/date_parsing.hpp  
>   
> Anyway, the c++98 compatible solutions can already be found in the library — you just need to make the statics part of a template — here’s one of the examples from the facet code — it’s a bit strange but it works.  The facets have been header only since they came into the library.  
>   
> https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/date_names_put.hpp  
> //part of a template...  
> static const char_type default_special_value_names[3][17];    
> …  
>   
> template<class Config, class charT, class OutputIterator>  
> const typename date_names_put<Config, charT, OutputIterator>::char_type  
> date_names_put<Config, charT, OutputIterator>::default_special_value_names[3][17] = {  
> {'n','o','t','-','a','-','d','a','t','e','-','t','i','m','e'},  
> {'-','i','n','f','i','n','i','t','y'},  
> {'+','i','n','f','i','n','i','t','y'} };    
>   
> Anyway hope that helps

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-04-05 17:58:56 UTC  
> Url: https://github.com/boostorg/date_time/issues/85#issuecomment-609456932  

I ended up doing this differently than I expected, but the work is completed for release 1.73 under issue #134.
