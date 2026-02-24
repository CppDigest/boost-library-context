# #62 - References to removed iterator_traits metafunction [Closed]

> Username: jefftrull  
> Created at: 2020-02-07 22:25:11 UTC  
> Updated at: 2020-02-10 01:48:41 UTC  
> Closed at: 2020-02-10 01:48:41 UTC  
> Url: https://github.com/boostorg/wave/issues/62  

Wave refers to `boost::detail::iterator_traits<Iterator>::value_type` in three places; that API was removed some time ago. The correct function is `boost::iterators::iterator_value<Iterator>::type`.  
  
This issue prevents the lexertl instantiation module from building successfully.
