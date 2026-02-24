# #2671 - client-only permessage-deflate still sends server headers [Open]

> Username: bgemmill  
> Created at: 2023-04-21 17:57:40 UTC  
> Updated at: 2025-03-06 20:39:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2671  

I'm using boost 1.81 and beast to connect as a client to a remote websocket host, and with compression disabled, everything works fine.  
  
This endpoint supports permessage-deflate from clients, and will also fail to upgrade if any server deflate headers are included.  
  
If I try to add compression like so:  
```  
    auto ret = std::make_shared<websocket_t>(io_ctx, ssl_ctx);  
    boost::beast::websocket::permessage_deflate pd_option{false, true};  
    ret->set_option(pd_option);  
```  
  
The upgrade headers still include `server_max_window_bits`, and the websocket upgrade is rejected. The issue seems to be in the translation between `pd_option` and beast's internal `pmd_offer`; the latter seems to support client and server at once only.  
  
Attempts to set the server window size to 0 to prevent the field from being written (looking at the source of pmd_extension.ipp) yield an invalid value error.  
  
Would it be possible for `pmd_offer` to respect the `pd_option`'s server_enable flag and not send those headers?

---

## Comment 1

> Username: bgemmill  
> Created at: 2023-04-22 03:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2671#issuecomment-1518496764  

Decorators do the job, but just feel super clunky since the option to use client or server compression was all ready specified.  
```  
    // Set a decorator to manually modify the websocket upgrade request for one-sided PMD negotiation  
    ret->set_option(boost::beast::websocket::stream_base::decorator(  
      [pd_option](boost::beast::websocket::request_type& req) {  
  
        std::string deflate_header("permessage-deflate;");  
        if(pd_option.server_enable) {  
          deflate_header.append(" server_max_window_bits=");  
          deflate_header.append(std::to_string(pd_option.server_max_window_bits));  
        }  
        if(pd_option.client_enable) {  
          deflate_header.append(" client_max_window_bits=");  
          deflate_header.append(std::to_string(pd_option.client_max_window_bits));  
        }  
  
        req.set(boost::beast::http::field::sec_websocket_extensions, deflate_header);  
      }));  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-04-24 02:21:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2671#issuecomment-1519291180  

have you tried setting `server_max_window_bits` on the `pd_option` struct ?

---

## Comment 3

> Username: bgemmill  
> Created at: 2023-04-24 02:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2671#issuecomment-1519305662  

@klemens-morgenstern yes, setting it to anything in a valid range has it sent as a header, and attempting to set it to an an invalid value gives beast errors about invalid values.  
  
Aside from the decorator above, I'm not sure how to only send the client PMD header.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-05-10 23:29:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2671#issuecomment-1542932147  

Sorry for the late response.  
  
The decorator solves it?

---

## Comment 5

> Username: bgemmill  
> Created at: 2023-05-12 02:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2671#issuecomment-1545004112  

While I can suppress the server headers with the decorator, it seems strange that they're sent at all when we specify no server headers in the `permessage_deflate pd_option`

---

## Comment 6

> Username: KaiPetzke  
> Created at: 2025-03-06 20:39:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2671#issuecomment-2704890841  

I have come over this problem recently, too. A server, that I had been using happily with permessage_deflate for a year suddenly changed its behavior and did no longer accept handshake with my boost beast websocket client, when I tried to use compression. Instead, it failed the handshake with HTTP status 400 and this message body:  
  
> unsupported permessage-deflate parameter: "server_max_window_bits=15"  
  
Looking at RFC 7692 and the parsing of the pmd header in `boost/beast/websocket/detail/pmd_extension.ipp`, I learned a few things:  
  
* A websocket server implementation may actually decide to refuse to implement the  `permessage_deflate` extension parameter `server_max_window_bits=NN`. If the client uses it nonetheless, the server may righteously refuse the connection!  
* The RFC therefore recommends clients to include a fallback offer without `server_max_window_bits` like this:  
>          Sec-WebSocket-Extensions:  
>            permessage-deflate; client_max_window_bits; server_max_window_bits=NN,  
>            permessage-deflate; client_max_window_bits  
* Even, if the client sent a request without `server_max_window_bits=NN`, the server still may use this parameter in its response. If the client sent this parameter, the server must include it in its response, and must use a value for NN, that is not higher than the one requested by the client.  
* Things are a bit different for `client_max_window_bits`: The client can send this parameter with or without the actual number of bits `=NN`. When using the parameter without `=NN`, the client indicates, that it understands this parameter and that the server can choose any value it wants and that the client will respect it. When the full parameter `client_max_window_bits=NN` is present in the request, the server has a wide range of choices: It may use `NN`, it may lower `NN` (in which case, it must include `client_max_window_bits=NN` in the response, which is otherwise an optional parameter in the response) or it may even ignore `NN` and just allocate the maximum 2^15 = 32,768 Byte window for decoding client messages. The handshake will fail, if the server cannot allocate a full size 2^15 Byte window, but the client didn't send the `client_max_window_bits` parameter, which then forbids the server from answering with it.  
* The implementation in the above mentioned file `pmd_extension.ipp` uses the special values `0` to signal _don't send_ and `-1` to signal _send without value_. The `-1` case is provided for both `server_max_window_bits` and `client_max_window_bits`, even though using the parameter without value is **not** legal for `server_max_window_bits` and for `client_max_window_bits` only in the request, but not in the response.  
* However, a security check in `boost/beast/websocket/detail/impl_base.hpp` throws an `invalid_argument` exception, if those special values of `0` or `-1` are used.  
  
Considering today's typical network speeds and memory sizes, `client/server_max_window_bits` seems rather anachronistic and it is understandable to me, that some new server implementations apparently choose to stop supporting this negotiation. There are a few possibilities to react to this situation:  
  
1. Start to generally leave out unnecessary parameters on PMD requests. This would mean:  
    * If `server_max_window_bits` is set to 15 in a websocket client request, don't include the parameter `server_max_window_bits` in the request at all. After all, the server may still send this parameter in its response, if it uses a smaller window size and wants to tell the client about that, so that it can save a few bytes.  
    * If `client_max_window_bits` is set to 15 in a client request, just include the tag `client_max_window_bits` in the request to tell the server, that the client understands this parameter and that the server can choose a smaller window size, if it needs to.  
2. Provide a fallback configuration for `permessage-deflate` as recommended by RFC 7692 and shown above. The fallback would be constructed by using the omissions laid out in option 1. before.  
3. Allow websocket client applications to set the special values `0` and `-1` for `client_max_window_bits` and `0` for `server_max_window_bits` via `websocket::stream<SOCKETTYPE>::set_option()`. The most useful combination is probably `server_max_window_bits = 0` and `client_max_window_bits = -1` and the documentation should be updated to encourage websocket client application developers to use these values.  
  
In my opinion, variant 2 is most complex and has the risk to break existing client applications, should some websocket servers out there not understand the fallback configuration (despite that that ability is mandated by RFC 7692).  
  
Variant 1 would be rather easy to implement and would mean, that all websocket client application developers, that use the default `server_max_window_bits = 15` and `client_max_window_bits = 15` immediately benefit from the improved behavior without any need to change their code. I attach sample code for variant 1 in `patch1.txt`. However, there is a small risk, that things break on non-confirming websocket servers and application developers might then be required to use an old version of boost::beast or use sub-optimal compression parameters to still enable PMD with those servers.  
  
Variant 3 would even be easier to implement, at least for the client side, as it is only necessary to relax the checks on setting `server_max_window_bits` and `client_max_window_bits`. There is already provision in the code to later sanitize those `0` and `-1` values to `15` instead, see function `pmd_normalize()`.  
  
However, if `0` and/or `-1` are used instead of `15`, if a given websocket runs in the server role, this will break the code in `pmd_negotiate_impl()`, which merges an external client's request and the local server's config. I have included an update to this function in `patch3.txt` as well, that normalizes the values of `server_max_window_bits` and/or `client_max_window_bits`, should they have been set to `0` or `-1`.  
  
**Summary**  
  
Currently, boost::beast::websocket sends an unnecessary complex `permessage-deflate` proposal as client, if `server_max_window_bits` and/or `client_max_window_bits` are set to their default 15. This breaks compression with some remote sites, as otherwise, this issue wouldn't have been opened. And servers, that don't like those unnecessary parameters seem to even got more common, because otherwise, I wouldn't have suddenly run into issues with boost::beast::websocket connections to a site, that was working fine before.  
  
There are two (three) fixes: Variant 1 is to just leave out the unnecessary extension parameters from the headers and hope, that doing so doesn't break existing applications (which they shouldn't, but who knows...). For details, see patch1.txt.  
  
Variant 2 (not recommended by me) would be to offer two connection variants for the server to choose from, one with the full parameter set and one with the reduced parameter set. Again, this shouldn't break things, if all servers were fully RFC 7692 conforming, but who knows.  
  
Variant 3 is to give the application developer full control to either send or leave out unnecessary parameters by using already existing logic in boost::beast::websocket, but relaxing some parameter checks to let the application developer also use those alternatives. Variant 3 also requires a small update to the code for the websocket server role to sanitize `server_max_window_bits` and `client_max_window_bits` provided by the application.  
  
Variant 3 opens the possibility, that websocket client developers test the changes, and, if there are no reports about websocket servers not working with the new code, we can later change the defaults for  `server_max_window_bits` and `client_max_window_bits` to `0` and `-1`, respectively, so that the change will then be rolled out to everybody, who uses the defaults. But even after that change, app developers will still be able to go back to `15` and `15` to get back the old behavior, if they need it.  
  
I haven't submitted a pull request, as there are two distinct ways to solve this issue and I don't know, which will be preferred by the maintainers.  
  
[patch1.txt](https://github.com/user-attachments/files/19114270/patch1.txt)  
  
[patch3.txt](https://github.com/user-attachments/files/19114662/patch3.txt)
