# #666 - bad parsing of portnumber [Closed]

> Username: JohannesGajdosikPKE  
> Created at: 2023-01-19 15:40:20 UTC  
> Updated at: 2023-01-25 13:24:49 UTC  
> Closed at: 2023-01-25 13:24:49 UTC  
> Url: https://github.com/boostorg/url/issues/666  

https://godbolt.org/z/9v37YqTbM  
  
"If a port is present and the numerical value is representable, it is returned ... Otherwise zero is returned" says the doc.  
The current code returns a fantasy number when the value overflows.
