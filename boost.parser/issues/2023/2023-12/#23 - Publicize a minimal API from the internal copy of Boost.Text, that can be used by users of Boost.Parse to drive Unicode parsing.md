# #23 - Publicize a minimal API from the internal copy of Boost.Text, that can be used by users of Boost.Parse to drive Unicode parsing [Closed]

> Username: tzlaine  
> Created at: 2023-12-03 21:27:53 UTC  
> Updated at: 2023-12-22 00:23:12 UTC  
> Closed at: 2023-12-22 00:23:12 UTC  
> Url: https://github.com/boostorg/parser/issues/23  

The docs assume that Boost.Text made it into Boost before this library is considered.  Because it did not, `text::as_utf32()` is not available to select the Unicode code path.
