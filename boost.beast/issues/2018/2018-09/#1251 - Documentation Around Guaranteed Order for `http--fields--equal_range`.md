# #1251 - Documentation Around Guaranteed Order for `http::fields::equal_range` [Closed]

> Username: cmazakas  
> Created at: 2018-09-19 18:01:00 UTC  
> Updated at: 2022-06-22 19:56:26 UTC  
> Closed at: 2022-06-22 19:56:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1251  

The `equal_range` member function internally guarantees that the range's left-to-right traversal matches the field ordering found in the message.  
  
This is an undocumented feature which can lead to users assuming the range is unordered which would create needless routines based around being unordered.
