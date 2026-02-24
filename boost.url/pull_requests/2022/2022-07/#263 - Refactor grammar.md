# #263 Refactor grammar: [Closed]

> Username: vinniefalco  
> Created at: 2022-07-28 02:32:57 UTC  
> Updated at: 2022-07-28 02:50:25 UTC  
> Closed at: 2022-07-28 02:50:25 UTC  
> Url: https://github.com/boostorg/url/pull/263  

The Rule concept is changed:  
  
* rules are stateful values  
* nested value_type holds the result of parsing  
* member function `Rule::parse` is the algorithm  
* parse returns `result<value_type>`  
  
And:  
  
* All rfc3986 rules are reimplemented  
* New grammar non-terminal elements introduced:  
  - char_rule  
  - not_empty_rule  
  - optional_rule  
  - sequence_rule  
  - variant_rule  
   
Not included:  
  
* Javadocs  
* Exposition  
* Examples

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-07-28 02:39:55 UTC  
> Url: https://github.com/boostorg/url/pull/263#issuecomment-1197586232  

An automated preview of the documentation is available at [https://263.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://263.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
