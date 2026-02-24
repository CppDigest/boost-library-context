# #1516 - file_win32 ops not used with beast::basic_stream [Open]

> Username: vinniefalco  
> Created at: 2019-03-13 00:40:22 UTC  
> Updated at: 2019-03-13 00:40:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1516  

The overloads of `http::write` and `http::async_write` for doing direct win32 file transfers are skipped when using `beast::basic_stream`, because the socket type does not match. I'm not sure this optimization is possible, at least not in the current form, because it would break the rate policy and the timeout feature.  
  
Users who care more about native `::TransmitFile` can simply use `net::basic_stream_socket` and forgo the features in `beast::basic_stream`. More research is needed into a solution, or if this can be solved at all.
