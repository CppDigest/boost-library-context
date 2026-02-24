# #2179 - How to enable OCSP stapling [Closed]

> Username: d3roch4  
> Created at: 2021-03-05 01:51:27 UTC  
> Updated at: 2022-01-09 05:17:18 UTC  
> Closed at: 2022-01-09 05:17:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2179  

I have to enable [OCSP stapling](https://en.wikipedia.org/wiki/OCSP_stapling) in my server implemented with beast.  
I am developement a [http server](https://github.com/d3roch4/httpserver) with beast and the performance is slow on https   
whe run command ```openssl s_client -connect localhost:4001 -status``` the result is: `OCSP response: no response sent`  
  
help me

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-05 09:48:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2179#issuecomment-791305476  

This is really a question about OpenSSL  
  
I did a quick search and came up witha few trails you may want to follow:  
[https://stackoverflow.com/questions/56253312/how-to-create-ocsp-request-using-openssl-in-c](https://stackoverflow.com/questions/56253312/how-to-create-ocsp-request-using-openssl-in-c)  
  
[https://akshayranganath.github.io/OCSP-Validation-With-Openssl/](https://akshayranganath.github.io/OCSP-Validation-With-Openssl/)  
  
note that the OpenSSL api is synchronous, so you may want to dispatch this work to a backround thread if your program is asynchronous.

---

## Comment 2

> Username: d3roch4  
> Created at: 2021-03-05 14:55:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2179#issuecomment-791470125  

I found this: https://stackoverflow.com/a/11591672/3795295  
  
```  
static int ocsp_resp_cb(SSL *s, void *arg)  
{  
const unsigned char *p;  
int len;  
OCSP_RESPONSE *rsp;  
len = SSL_get_tlsext_status_ocsp_resp(s, &p);  
BIO_puts(arg, "OCSP response: ");  
if (!p)  
    {  
    BIO_puts(arg, "no response sent\n");  
    return 1;  
    }  
rsp = d2i_OCSP_RESPONSE(NULL, &p, len);  
if (!rsp)  
    {  
    BIO_puts(arg, "response parse error\n");  
    BIO_dump_indent(arg, (char *)p, len, 4);  
return 0;  
}  
BIO_puts(arg, "\n======================================\n");  
OCSP_RESPONSE_print(arg, rsp, 0);  
BIO_puts(arg, "======================================\n");  
OCSP_RESPONSE_free(rsp);  
return 1;  
}  
```  
  
how do i register the callback?  
But where should I write? before the handshake?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2179#issuecomment-850857866  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2179#issuecomment-1008232396  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
