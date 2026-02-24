# #422 Added missing BOOST_ASIO_DECL [Open]

> Username: OskarSigvardsson  
> Created at: 2023-10-12 11:29:15 UTC  
> Updated at: 2023-10-12 11:29:15 UTC  
> Url: https://github.com/boostorg/asio/pull/422  

We are making an audio plugin that uses Boost ASIO and Boost Beast for WebSocket support and we discovered quite a serious crash when our plugin was loaded in to Ableton Live (a very popular Digital Audio Workstation). After digging into it for a while, we discovered this forum post on the JUCE forums (JUCE is a popular framework for making audio plugins), where some other users had almost exactly the same issue:  
  
https://forum.juce.com/t/boost-crashing-ableton-on-mac-os/54867  
  
One user there, Cymatic, noticed that in service_registry.hpp, there appeared to be a missing `BOOST_ASIO_DECL` marker on one of the functions. This appeared to cause some linker issues.  
  
I tried this fix in our project, and indeed it does solve the issue. I'm still a little unclear as to why since we build statically and header-only, but presumably it causes a missing `inline` which makes for an ODR violation, or something of that nature. In any case: it does just LOOK wrong, since the `destroy` function is marked this way. I don't have enough insight into Boost at this deep level to be able to explain why it works in more detail, but it does seem to do the trick.  
  
Since it appears the original solver hadn't upstreamed the fix, I figure I should submit a PR for it.  
  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---
