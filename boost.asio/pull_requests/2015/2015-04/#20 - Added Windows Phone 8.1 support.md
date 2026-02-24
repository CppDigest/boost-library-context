# #20 Added Windows Phone 8.1 support [Closed]

> Username: mosherubin  
> Created at: 2015-04-30 10:53:29 UTC  
> Updated at: 2017-12-02 07:51:40 UTC  
> Closed at: 2017-12-02 07:51:40 UTC  
> Url: https://github.com/boostorg/asio/pull/20  

1. Added BOOST_ASIO_WINDOWS_RUNTIME to several #ifdef blocks for Windows Phone 8.1  
2. Uses parallel function calls for WP8.1 (e.g., CreateEventEx() rather than CreateEvent())  
3. mswsock.h and mswsock.lib not used for Windows Phone  
4. WinRT socket now derives from reactive_socket_service, rather than a dedicated winrt_ssocket_service

---

## Comment 1

> Username: StSimmons  
> Created_at: 2015-11-30 13:49:11 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160632216  

I'm a little confused about how much of this works.  
1) In socket_types.pp you add some guards around mswsock etc, but that would never even get processed because of the `#if defined(BOOST_ASIO_WINDOWS_RUNTIME)  
 // Empty.` above.  
2) Your change to use reactive_socket_service in socket_acceptor_service.hpp can't build. reactive_socket_service includes socket_holder, socket_holder makes use of socket_ops::close which isn't defined for WinRT (explicitly excluded by the socket_ops.hpp/.ipp)  
  
Did you not experience these issues?

---

## Comment 2

> Username: mosherubin  
> Created_at: 2015-11-30 14:24:04 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160643810  

@StSimmons I think an apology on my part is in order.  Back in April 2015 I issued an ASIO pull request.  I subsequently was in contact with Chris Kohlhoff, discussing my changes with him.  I ultimately returned all my changes to his project (with back-translating for his project) -- I understood that that is the correct way to return my changes for ASIO.  He has since incorporated my changes to his ASIO project.  Please consider my April 2015 pull request to the Boost ASIO as deprecated.  
  
We now have two options:  
1. Wait for Chris to upload his ASIO project, translated for Boost.  
2. I can issue a pull request with the Boost ASIO I currently have, and which works perfectly for me and my company.  
  
What is your preference?  
  
Moshe

---

## Comment 3

> Username: StSimmons  
> Created_at: 2015-11-30 14:27:56 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160644878  

Ah, I see - that makes more sense :D   
  
I have little (read: none) authority here, but I personally would be exceptionally grateful if you could make a PR with your working version until such a point that Chris manages to move his version over into boost. Please let me know!  
  
Steve

---

## Comment 4

> Username: mosherubin  
> Created_at: 2015-11-30 14:37:09 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160647677  

@StSimmons  I would gladly do this but the work involved for me is considerable.  I made my changes on Boost 1.57, having to merge into the current head 1.59.  My other PRs (i.e., regex, filesystem, and interprocess) required much work to get them to merge and compile on 1.59 because of the large number of modifications in 1.59 itself.  I fear that asio, with the 15 files changed, will be a huge amount of error-prone work.  
  
Having said that, let me take a look at the work involved.  If it's feasible, I'll attempt it.

---

## Comment 5

> Username: StSimmons  
> Created_at: 2015-11-30 14:40:19 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160648488  

No problem at all!  
I can't see any of these style changes on Chris' project? Am I missing something, or is it just not in a public branch yet?  
An alternative, would it be possible to make public your 1.57 implementation? I am happy to work against that and can then push back to you for a release? I am really keen not to reinvent the wheel this week, and would LOVE a reference implementation.

---

## Comment 6

> Username: mosherubin  
> Created_at: 2015-11-30 14:44:16 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160649653  

How would you like me to make my 1.57 version available to you? At the time I downloaded the Boost source ZIP file, ported to Windows Phone, and used it within my company.  When I returned it to Chris forked his develop branch, then manually merged my changes to his.

---

## Comment 7

> Username: StSimmons  
> Created_at: 2015-11-30 14:46:12 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-160650333  

A zip would be fine, though others might see this and might want the same file - so it depends how public you want to make it. Perhaps a public repo on GitHub? Or otherwise, a zip and a link?

---

## Comment 8

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:51:40 UTC  
> Url: https://github.com/boostorg/asio/pull/20#issuecomment-348675644  

Closing as the related fix has already been applied.

---
