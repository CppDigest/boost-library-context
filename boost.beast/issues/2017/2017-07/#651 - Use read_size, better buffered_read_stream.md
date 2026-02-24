# #651 - Use read_size, better buffered_read_stream [Closed]

> Username: vinniefalco  
> Created at: 2017-07-14 23:06:21 UTC  
> Updated at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/651  

When size() and capacity_ are zero, buffered_read_stream can avoid creating a composed operation.  
  
Calls to prepare(), should use `read_size(buffer_, capacity_)`
