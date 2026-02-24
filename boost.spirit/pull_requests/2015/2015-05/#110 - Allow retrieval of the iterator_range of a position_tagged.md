# #110 Allow retrieval of the iterator_range of a position_tagged. [Merged]

> Username: mlang  
> Created at: 2015-05-29 09:11:38 UTC  
> Updated at: 2015-07-08 22:42:57 UTC  
> Merged at: 2015-07-08 22:42:57 UTC  
> Closed at: 2015-07-08 22:42:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/110  

position_cache allows to retrieve the iterator_range of a position tagged AST  
type.  However, pos_cache in error_handler<Iterator> is private, so the  
iterator_range can not be retrieved from client code. It would be useful for a  
"late raw" and for testing purposes. This patch adds  
error_handler<Iterator>::position_of(position_tagged).

---
