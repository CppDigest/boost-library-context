# #18 - noexcept mode [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-07-14 07:56:45 UTC  
> Updated at: 2024-05-01 09:44:32 UTC  
> Closed at: 2024-05-01 09:44:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/18  

It would be possible to add a noexcept mode, that turns everything into a `system::result` with error_codes.   
The issue will however be allocation failure, which makes the design a bit more tricky.   
  
Additionally, this library relies on asio, so I couldn't convince myself yet that this is a good match for this library and shouldn't be a fork, like `embedded-async`.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-05-01 09:44:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/18#issuecomment-2088220521  

Should be a different library, but compiling with `no-exceptions` works now.
