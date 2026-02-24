# #42 Request support for raw DEFLATE used in websocket permessage-deflate extension [Closed]

> Username: WeissGotsman  
> Created at: 2017-09-28 13:16:22 UTC  
> Updated at: 2017-09-28 15:11:41 UTC  
> Closed at: 2017-09-28 14:56:51 UTC  
> Url: https://github.com/boostorg/iostreams/pull/42  

Request support for raw DEFLATE used in websocket permessage-deflate extension.  
  
iostreams offers the cleanest api to compress/decompress    
  
1. zlib (a.k.a. DEFLATE with a zlib wrapper)  
2. gzip (a.k.a. DEFLATE with a gzip wrapper)  
  
but it lacks support for compress/decompress raw DEFLATE (with no header and checksum), such as used in in websocket permessage-deflate extension(https://tools.ietf.org/html/rfc7692). a quick workaround for decompress is to modify zlib.hpp and then decompress in the same ways as zlib.  
  
from   
  
```  
template<typename Alloc>  
bool zlib_decompressor_impl<Alloc>::filter  
    ( const char*& src_begin, const char* src_end,  
      char*& dest_begin, char* dest_end, bool /* flush */ )  
{  
    before(src_begin, src_end, dest_begin, dest_end);  
    int result = xinflate(zlib::sync_flush);  
    after(src_begin, dest_begin, false);  
    zlib_error::check BOOST_PREVENT_MACRO_SUBSTITUTION(result);  
    return !(eof_ = result == zlib::stream_end);  
}  
```  
  
to  
  
```  
template<typename Alloc>  
bool zlib_decompressor_impl<Alloc>::filter  
    ( const char*& src_begin, const char* src_end,  
      char*& dest_begin, char* dest_end, bool /* flush */ )  
{  
    if (src_begin == src_end)  
		return false;  
    before(src_begin, src_end, dest_begin, dest_end);  
    int result = xinflate(zlib::sync_flush);  
    after(src_begin, dest_begin, false);  
    zlib_error::check BOOST_PREVENT_MACRO_SUBSTITUTION(result);  
    return !(eof_ = result == zlib::stream_end);  
}  
```  
  
existing websocket library that use zlib for DEFLATE returns right after get Z_OK (not Z_STREAM_END), the check of src_begin == src_end prevents Z_BUF_ERROR.  
https://github.com/zaphoyd/websocketpp/blob/378437aecdcb1dfe62096ffd5d944bf1f640ccc3/websocketpp/extensions/permessage_deflate/enabled.hpp  
https://github.com/warmcat/libwebsockets/blob/63ebeaa53e4ed96b666bfcabe1f2ad940bb74e6e/lib/extension-permessage-deflate.c

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-09-28 14:56:51 UTC  
> Url: https://github.com/boostorg/iostreams/pull/42#issuecomment-332863285  

Not only are some of the checks not passing but I do not understand the list of merges. We do not merge from 'master' to 'develop' on PRs. We merge pull requests into 'develop' from someone else's changes to the 'develop' branch. I am closing this PR. Please try again with some reasonable list of changes in your own fork of the 'develop' branch.

---

## Comment 2

> Username: WeissGotsman  
> Created_at: 2017-09-28 15:09:30 UTC  
> Updated_at: 2017-09-28 15:11:41 UTC  
> Url: https://github.com/boostorg/iostreams/pull/42#issuecomment-332867251  

Note that this is a feature request. I put it here because this repo does not allow me to post an issue, and I'm not intend to commit my change because it is rather a temporary solution.   
  
waiting someone to catch up and post a final solution because websocket permessage-deflate is widely adopted today.

---
