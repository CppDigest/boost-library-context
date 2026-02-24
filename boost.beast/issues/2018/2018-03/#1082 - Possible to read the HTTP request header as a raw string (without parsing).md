# #1082 - Possible to read the HTTP request header as a raw string (without parsing)? [Closed]

> Username: carolinebeltran  
> Created at: 2018-03-31 05:15:01 UTC  
> Updated at: 2018-03-31 16:20:51 UTC  
> Closed at: 2018-03-31 16:20:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1082  

Beast automatically parses and stores the request headers into a container.  Field names are mapped to an enum.  I think that this is very helpful behavior when working with HTTP.  
  
1) If the client sends a custom header that has no associated enum mapped to it, then you get **unknown-field=some_value**.  I was planning to add a **custom HTTP header** but then the server will not be able to map to it.  I don’t want to add it to Beast source because I may forget to update with the new Beast releases.  
  
2) By default, headers are stored within an ordered container and if you loop through each element, the headers will be reordered alphabetically.  _UPDATE: to avoid confusion, I would like to mention that I was wrong about this item.  As indicated by Vinnie in his answer below, headers are not reordered by Beast._  
  
I had hoped to find something similar to the **body()** method, but that would instead allow for us to **read the entire HTTP ‘raw’ header as a string**.  I have a special situation where I can really use the ‘raw’ and unordered HTTP header that the client sends to my Beast server.    
  
Secondly, is there a Beast facility to write custom (arbitrary) HTTP headers?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-31 06:05:08 UTC  
> Updated at: 2018-03-31 06:06:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1082#issuecomment-377669215  

>Field names are mapped to an enum.  
  
Field names are mapped to an enumeration only if the field name is one that is known to Beast. Otherwise, it will be mapped to the special value `field::unknown` as you pointed out. However, Beast still stores the original, unmodified field name. The `value_type` for a field contains three things:  
  
* The enumeration if the field is known, returned by `value_type::name`  
* The name of the field as a string, returned by `value_type::name_string`  
* The text corresponding to the field value, returned by `value_type::value()`  
  
This is documented:  
  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields__value_type.html  
  
> I was planning to add a custom HTTP header  
  
Of course you can do that, just insert the field into the header using the overload which accepts a `string_view` for the name, also documented:  
  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/insert/overload2.html  
  
> By default, headers are stored within an ordered container and if you loop through each element, the headers will be reordered alphabetically  
  
The implementation stores fields in both a set and a list. The set is in alphabetical order using an rb-tree, for fast lookups. The list is in the original order of appearance, except for the rule that if a field name appears more than once, they will appear consecutive in the list. List iterators are returned by `basic_fields::begin` and `basic_fields::end`.  
  
To the point, iterating the headers in a `basic_fields` will visit them in the order they appeared in the serialized message.  
  
>...allow for us to read the entire HTTP ‘raw’ header as a string  
  
LOL no...that wouldn't be very friendly at all! Fortunately it should not be necessary as Beast has sufficient functionality to do what you need.  
  
> is there a Beast facility to write custom (arbitrary) HTTP headers?  
  
Yes, simply use the correct overload of `insert` and you can specify any string you want.

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-03-31 14:32:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1082#issuecomment-377697084  

Vinnie, thank you so much for pointing out things that I had missed in the documentation, I thought that I was going to have to do some raw header manipulation myself but Beast did in fact provide all of the desired functionality.
