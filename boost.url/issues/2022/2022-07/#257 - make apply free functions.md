# #257 - make apply free functions [Closed]

> Username: vinniefalco  
> Created at: 2022-07-27 20:43:58 UTC  
> Updated at: 2022-07-30 02:19:14 UTC  
> Closed at: 2022-07-30 02:19:14 UTC  
> Url: https://github.com/boostorg/url/issues/257  

Private member `apply` in `url_view` and `authority_view` are ugly as they require including the rule header in the view header. We can refactor this by friending struct `detail::apply` and adding member functions which take the view and the parsed element as parameters, thus ensuring that only .ipp files will have to include the rule headers.
