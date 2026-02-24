# #403 - Signals not received when building with link time optimization enabled and using asio as static library [Open]

> Username: ni-tschuett  
> Created at: 2022-11-22 16:31:32 UTC  
> Updated at: 2022-11-23 01:48:31 UTC  
> Url: https://github.com/boostorg/asio/issues/403  

We are using asio as a static library instead of header only, boost version 1.78. We instantiate a `boost::asio::signal_set` in the main thread of our application but do not receive any signals when building with link time optimization enabled (using gcc9/10). With making `get_signal_state()` (signal_set_service.ipp) `BOOST_NOINLINE` the issue does not occur and signals are again received properly.

---

## Comment 1

> Username: timblechmann  
> Created at: 2022-11-23 01:48:30 UTC  
> Url: https://github.com/boostorg/asio/issues/403#issuecomment-1324448282  

the `NOINLINE` is a compiler barrier here. makes me wonder if there's any missing `volatile` (especially when meditating about https://stackoverflow.com/a/57679370)
