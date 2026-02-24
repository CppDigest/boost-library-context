# #256 - Make most rfc/ rules private [Closed]

> Username: vinniefalco  
> Created at: 2022-07-27 20:20:56 UTC  
> Updated at: 2022-07-30 02:19:14 UTC  
> Closed at: 2022-07-30 02:19:14 UTC  
> Url: https://github.com/boostorg/url/issues/256  

Users don't need the big composite rules like absolute-uri, or basically anything that is already achieved through construction of `url_view` or `authority_view`. However we can publish the smaller pieces, like `pct_encoding_rule` since those can be useful.
