# #936 - What is the proper way to build boost on macOS universally? [Open]

> Username: Mardalemer  
> Created at: 2024-08-12 11:39:10 UTC  
> Updated at: 2024-08-12 11:39:10 UTC  
> Url: https://github.com/boostorg/boost/issues/936  

I'm trying to build several boost libraries like universal binaries for arm64 and x86_64 architectures. Firstly I've built it by providing for b2 two archs at once like this:  
  
`./b2 -arch x86_64 -arch arm64`  
  
but it doesn't work, because of `boost-context` cross-compilation. There is some assembly code and I feel that it needs some clarifying params, like: `<architecture>`, `<address-model>`, `<binary-format>` and `<abi>`. So build fails.  
  
I try to build two slim binaries and with `lipo` create universal one.  
  
Building `x86_64` slim library on Macbook with `Apple Silicone` fails for `boost-locale` and `boost-stacktrace` libraries. As I understand about `boost-locale`, it can not find `libiconv` while building slim library.  
  
So the question is, what's the proper way to build universal binaries for macOS using `b2`?
