# #19 Added new trim_whitespace_without_normalization option [Open]

> Username: timostrunk  
> Created at: 2016-02-17 13:13:27 UTC  
> Updated at: 2016-02-17 13:13:27 UTC  
> Url: https://github.com/boostorg/property_tree/pull/19  

Following our discussion here: https://svn.boost.org/trac/boost/ticket/11600#comment:6  
I implemented a new flag trim_whitespace_without_normalization, which enables rapidxml::parse_trim_whitespace without enabling rapidxml::normalize_whitespace .  
  
This option allows preserving whitespace in textnodes and pretty printing of XML at the same time (and clean roundtrips of those).  
  
If both "boost::property_tree::xml_parser::trim_whitespace" and "boost::property_tree::xml_parser::trim_whitespace_without_normalization" are given "trim_whitespace" overrides "trim_whitespace_without_normalization". Existing behaviour of the library is not modified.

---
