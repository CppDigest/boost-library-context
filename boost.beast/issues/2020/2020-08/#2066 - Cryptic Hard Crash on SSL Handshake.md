# #2066 - Cryptic Hard Crash on SSL Handshake [Closed]

> Username: jakepenn  
> Created at: 2020-08-25 22:41:15 UTC  
> Updated at: 2020-09-15 07:21:15 UTC  
> Closed at: 2020-09-15 07:21:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2066  

Hello, I'm attempting to get beast running a secure socket inside of an audio plugin.   
  
I've developed the socket as a stand-alone application and it's been working great, however when I shift to open the socket inside of a plugin format, I'm getting a very hard crash which shuts down the application completely and turns off the debugger inside of Xcode.   
  
I've been able to retrieve the stack trace from the system crash logs but I'm struggling to determine what might be the culprit as the only difference is standalone vs AU/VST3.  
  
The VST3 appears to be crashing on this line when running inside the Ableton DAW:  
  
`ws.next_layer().handshake(ssl::stream_base::client);`   
  
Here is the full stack trace:   
  
```  
1   com.ThaBoiz.SocketTest        	0x000000019ccf0847 boost::system::detail::failed_impl(int, boost::system::error_category const&) + 119 (error_code.hpp:423)  
2   com.ThaBoiz.SocketTest        	0x000000019ccf3de7 boost::system::error_code::error_code(int, boost::system::error_category const&) + 55 (error_code.hpp:589)  
3   com.ThaBoiz.SocketTest        	0x000000019ccf3da3 boost::system::error_code::error_code(int, boost::system::error_category const&) + 35 (error_code.hpp:591)  
4   com.ThaBoiz.SocketTest        	0x000000019cc6a205 boost::asio::error::make_error_code(boost::asio::error::misc_errors) + 37 (error.hpp:325)  
5   com.ThaBoiz.SocketTest        	0x000000019cc6b113 boost::system::error_code::error_code<boost::asio::error::misc_errors>(boost::asio::error::misc_errors, boost::system::detail::enable_if<is_error_code_enum<boost::asio::error::misc_errors>::value, void>::type*) + 35 (error_code.hpp:596)  
6   com.ThaBoiz.SocketTest        	0x000000019cc6b0e3 boost::system::error_code::error_code<boost::asio::error::misc_errors>(boost::asio::error::misc_errors, boost::system::detail::enable_if<is_error_code_enum<boost::asio::error::misc_errors>::value, void>::type*) + 35 (error_code.hpp:597)  
7   com.ThaBoiz.SocketTest        	0x000000019cc69aa5 boost::asio::ssl::detail::engine::map_error_code(boost::system::error_code&) const + 53 (engine.ipp:195)  
8   com.ThaBoiz.SocketTest        	0x000000019cc760b9 unsigned long boost::asio::ssl::detail::io<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor>, boost::asio::ssl::detail::handshake_op>(boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor>&, boost::asio::ssl::detail::stream_core&, boost::asio::ssl::detail::handshake_op const&, boost::system::error_code&) + 649 (io.hpp:86)  
9   com.ThaBoiz.SocketTest        	0x000000019cc75dfe boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor> >::handshake(boost::asio::ssl::stream_base::handshake_type, boost::system::error_code&) + 78 (stream.hpp:375)  
10  com.ThaBoiz.SocketTest        	0x000000019cc75d83 boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor> >::handshake(boost::asio::ssl::stream_base::handshake_type) + 51 (stream.hpp:357)  
11  com.ThaBoiz.SocketTest        	0x000000019cc412cb boost::beast::ssl_stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor> >::handshake(boost::asio::ssl::stream_base::handshake_type) + 43 (ssl_stream.hpp:328)  
12  com.ThaBoiz.SocketTest        	0x000000019cc40778 MKUBoostSocket::initialize(juce::String, juce::String, juce::String) + 424 (MKUBoostSocket.cpp:44)  
13  com.ThaBoiz.SocketTest        	0x000000019cc40391 MKUBoostSocket::MKUBoostSocket() + 369 (MKUBoostSocket.cpp:15)  
14  com.ThaBoiz.SocketTest        	0x000000019cc40da5 MKUBoostSocket::MKUBoostSocket() + 21 (MKUBoostSocket.cpp:19)  
15  com.ThaBoiz.SocketTest        	0x000000019ccef5d7 SocketTestAudioProcessor::SocketTestAudioProcessor() + 103 (PluginProcessor.cpp:15)  
16  com.ThaBoiz.SocketTest        	0x000000019ccef6a5 SocketTestAudioProcessor::SocketTestAudioProcessor() + 21 (PluginProcessor.cpp:21)  
17  com.ThaBoiz.SocketTest        	0x000000019ccefc18 createPluginFilter() + 40 (PluginProcessor.cpp:183)  
18  com.ThaBoiz.SocketTest        	0x000000019cbf6cb8 juce::createPluginFilterOfType(juce::AudioProcessor::WrapperType) + 24 (juce_CreatePluginFilter.h:41)  
19  com.ThaBoiz.SocketTest        	0x000000019cbf694d juce::JuceVST3Component::JuceVST3Component(Steinberg::Vst::IHostApplication*) + 221 (juce_VST3_Wrapper.cpp:1683)  
20  com.ThaBoiz.SocketTest        	0x000000019cbf685d juce::JuceVST3Component::JuceVST3Component(Steinberg::Vst::IHostApplication*) + 29 (juce_VST3_Wrapper.cpp:1713)  
21  com.ThaBoiz.SocketTest        	0x000000019cbea950 juce::createComponentInstance(Steinberg::Vst::IHostApplication*) + 48 (juce_VST3_Wrapper.cpp:3084)  
22  com.ThaBoiz.SocketTest        	0x000000019cbf369c juce::JucePluginFactory::createInstance(char const*, char const*, void**) + 524 (juce_VST3_Wrapper.cpp:3211)  
```  
  
Of course I'm not asking for anyone to write my code, however the behavior exhibited seems like some sort of memory overrun which is quickly getting shut down. It appears the function should return an error code if there's a problem but that's not occurring. It's also wrapped up inside of a try / catch, but still fails / crashes without getting the catch branches.   
  
At this point I'm open to any tips or direction!  
  
Thanks,   
  
Jake

---

## Comment 1

> Username: pdimov  
> Created at: 2020-08-25 23:02:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-680310736  

Check whether VST3 is using Boost (is it linked to libboost_system.so maybe?) and if so, which version.

---

## Comment 2

> Username: jakepenn  
> Created at: 2020-08-25 23:06:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-680311964  

Hmm that's an interesting idea... VST3 would not being using boost as the wrapper code is distributed and the source doesn't contain boost or depend on it being installed on the compiling system, however it's very likely that Ableton the DAW may be using boost.   
  
Would it make sense that something like this would cause a conflict?

---

## Comment 3

> Username: pdimov  
> Created at: 2020-08-25 23:17:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-680315385  

Right, I meant the host software.  
  
It's possible, as the crash occurs in Boost.System, which might in principle be caused by two incompatible Boost versions existing in the same process.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-08-26 00:14:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-680338408  

More specifically, the crash occurs in `detail::failed_impl`, which calls a virtual function `failed` in `boost::system::error_category`. This virtual function was introduced in Boost 1.69. It appears that the `error_category` object on which it's called somehow comes from an earlier Boost release, and as a result, there's no function pointer at that vtable slot.  
  
The `error_category` in question comes from Asio, specifically https://github.com/boostorg/asio/blob/94416dec936da7c63576acf848181ea0ed0cc3fb/include/boost/asio/impl/error.ipp#L118. Why this manages to return an earlier `error_category` is anyone's guess. :-)  
  
One likely reason is that `error_category` is declared with `BOOST_SYMBOL_VISIBLE`, which expands to `__attribute__((visibility(default)))`. This means that the vtable has default visibility, so the linker would pick one. This is necessary in order to make things work properly in the presence of dynamic libraries, but prevents two incompatible Boost versions coexisting in the same process.

---

## Comment 5

> Username: jakepenn  
> Created at: 2020-09-04 04:23:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-686895899  

Thanks @pdimov -- I think you're totally right here. It seems like it's working everywhere outside of there. Is there a way to determine what version of boost they're using so I could match them?

---

## Comment 6

> Username: pdimov  
> Created at: 2020-09-04 10:57:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-687075346  

I can't think of one. Maybe just ask them.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-09-15 07:21:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2066#issuecomment-692520033  

I'm going to close as I don't have enough information to know whether this is a systemic or environmental bug. Please reopen if you get more information.
