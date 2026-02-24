# #1138 - More tolerant parsing of CRLF characters in http headers [Closed]

> Username: beast-lenient-parsing  
> Created at: 2018-05-22 16:24:14 UTC  
> Updated at: 2018-07-06 02:48:36 UTC  
> Closed at: 2018-07-06 02:48:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1138  

In using the beast http handler on a project, we've noticed the tolerance for unrecognized control characters to be rather strict. Specifically, we're seeing the \001 character embedded in some cookies arriving from certain parties, resulting in `bad value` reads from beast.  
  
Other similar http libraries seem to handle these responses.  
  
While it's clear that these characters are not in fact allowed per the RFC for HTTP, I'm curious if there's any plans to make beast more tolerant in it's header parsing?  
  
Here is the relevant code in question, specifically the flow resulting in the `goto found_control`  
  
```  
    static  
    char const*  
    parse_token_to_eol(  
        char const* p,  
        char const* last,  
        char const*& token_last,  
        error_code& ec)  
    {  
        for(;; ++p)  
        {  
            if(p >= last)  
            {  
                ec = error::need_more;  
                return p;  
            }  
            if(BOOST_UNLIKELY(! is_print(*p)))  
                if((BOOST_LIKELY(static_cast<  
                        unsigned char>(*p) < '\040') &&  
                    BOOST_LIKELY(*p != '\011')) ||  
                    BOOST_UNLIKELY(*p == '\177'))  
                    goto found_control;  
        }

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-22 18:54:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1138#issuecomment-391102433  

No plans to make the parser relaxed, but you can simply filter the input before passing it to Beast.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-30 01:38:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1138#issuecomment-393002491  

LOL, what is this, an account just for reporting one issue?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-06-29 02:38:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1138#issuecomment-401230239  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-07-06 02:48:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1138#issuecomment-402909816  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
