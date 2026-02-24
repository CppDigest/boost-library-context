# #2194 - Sample for proper handling of loading root certificates [Closed]

> Username: Tectu  
> Created at: 2021-03-16 11:15:44 UTC  
> Updated at: 2021-06-15 13:49:58 UTC  
> Closed at: 2021-06-15 13:49:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2194  

As the example under `/example/common/root_certificates.hpp` states, root certificates should be loaded through whatever mechanism the underlying OS provides.  
  
Is there any kind of sample code, snippet or even just a guideline to illustrate how to _properly_ do this? It seems that beast does provide some higher-level API for this which in turn relies on OpenSSL. However, I couldn't spot a full example / proper documentation (other than the API).  
  
I came across a few "blog post" talking about this but what I've seen doesn't inspire a lot of confidence. I'd like to find some sort of example or guideline preferably by beast itself or at least by somebody who has the experience of building real-world production ready applications rather than some copy-pasted together blog post.  
  
Are there any resources that are of value you guys can point me at?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-17 08:29:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-800895746  

Handling of root certificates is unfortunately OS-specific.   
It's outside the scope of boost.beast.  
  
I'm happy to do what I can to help you research what you need for your use case.

---

## Comment 2

> Username: Tectu  
> Created at: 2021-03-17 11:21:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-801003472  

Thank you for offering your help - It's greatly appreciated.  
  
# Background  
  
The scenario: After reading some documentation/posts/mails from Vinnie I understood that boost.beast is a low-level HTTP & WS library and is intended to stay this way. Furthermore, Vinnie actively hopes that somebody will eventually provide a higher-level library on top of boost beast. I am going to _attempt_ that.  
I already have three different applications built on top of boost.beast which all do similar things so there's a lot of code duplication and I think that by now I understand the boost.beast philosophy & design structure well enough to generalize this into a re-usable server/client library.  
  
Therefore, the goal is to write a library which:  
  - Provides an HTTP & WS server which supports both plain and TLS connections  
  - Provides an HTTP & WS client which supports both plain and TLS connections  
  - Runs on Windows, BSD, MacOS & Linux  
  - Compiles with GCC, Clang & MSVC  
  
To reach this goal, eventually handling certificates properly is important. I would like to already gather information on this to get a better picture of the situation before actually starting to commit to a specific design or solution - although I have the feeling that there won't be a lot of room for decisions.  
  
#  The problem at hand  
Basically I need to design a piece of C++ code which loads the root certificates provided by the underlying OS. This will inherently be OS dependent code as you pointed out.  
Depending on the complexity of the differences between the various OS implementations I'd either opt for a single piece of code with the necessary `#ifdef` fuckery or I would leverage CMake to select the appropriate code to compile for each platform.  
  
# Unstructured brabbling  
So, where to go from here? Can you provide me with rough guidelines regarding what I should be looking for/at?  
  
Lets start simple: I came across the existence of [`ssl::context::set_default_verify_paths()`](https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/reference/ssl__context/set_default_verify_paths.html).   
On the surface, that sounds as if it would already do quite a lot:   
  
> This function specifies that the context should use the default, system-dependent directories for locating certification authority certificates.  
  
How far does this get us? Is this a full implementation that already handles the underlying operating system implementation or does this only get us so far?  
  
To get back to boost.beast: If I understand correctly there's not really anything that needs to happen on the boost.beast side, right? Everything encryption related would directly be handled by boost.asio? Therefore, I also assume that there's no API or other functionality that ships with boost.beast that I have missed so far which would further help to get a step closer to the overall goal?  
  
Based on my very short & limited research it also seems like OpenSSL would handle most of the ugly stuff for us. Therefore, it should be possible to just ask OpenSSL to handle the certificate loading and pass the necessary information over to either boost.asio or boost.beast. But if that would be the case - surely this functionality would already ship with either boost.beast or boost.asio?  
  
# What would help  
The above most likely contains a lot of unnecessary information for you - it's more to be sure that the overall scenario is known.  
For actually making progress here, I think the following information would be very helpful to me:  
  - Understanding what [`ssl::context::set_default_verify_paths()`](https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/reference/ssl__context/set_default_verify_paths.html) actually does and what it doesn't do.  
  - Understanding which parts of this undertaking are relevant to boost.asio and which are relevant to boost.beast.  
  - Understanding which mechanisms/functionalities/tools are already provided (directly by boost) to facilitate this.  
  - Better understanding the process of "loading root certificates". It seems rather simple at first - but maybe this is a lot more complex than loading some certificates via an OS dependent interface?

---

## Comment 3

> Username: Tectu  
> Created at: 2021-03-25 16:20:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-807036972  

@madmongo1 bump

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-03-25 17:57:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-807189586  

Most functions on ssl::context are passthroughs to underlying openssl functions.  
  
https://www.openssl.org/docs/man1.1.0/man3/SSL_CTX_set_default_verify_paths.html  
  
This will almost certainly not load the system root certificates on windows or macos.  
  
I think some OS-specific trickery will be needed to load the certificates into memory. These can then be submitted to asio/openssl with   
  
https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/reference/ssl__context/use_certificate.html and  
  
https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/reference/ssl__context/use_certificate_chain.html

---

## Comment 5

> Username: WojciechMigda  
> Created at: 2021-05-07 10:52:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-834263462  

@Tectu , please check out this github project: https://github.com/djarek/certify  
I am using it in my projects and maybe it might be helpful for you too.  
  
A short snippet from my codebase that illustrates usage:  
```c++  
    // verify SSL context  
    {  
        ssl_ctx.set_verify_mode(boost::asio::ssl::verify_peer | boost::asio::ssl::context::verify_fail_if_no_peer_cert);  
        ssl_ctx.set_default_verify_paths();  
          
        boost::certify::enable_native_https_server_verification(ssl_ctx);  
    }  
```

---

## Comment 6

> Username: Tectu  
> Created at: 2021-05-07 14:16:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-834441992  

This looks very promising. I'll give that a try once I tackle this.  
Thank you very much for pointing me towards that!

---

## Comment 7

> Username: Tectu  
> Created at: 2021-06-15 13:49:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2194#issuecomment-861515264  

Using the `certify` library worked out well.
