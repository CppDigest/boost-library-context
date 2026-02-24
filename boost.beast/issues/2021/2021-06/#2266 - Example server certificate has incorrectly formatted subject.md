# #2266 - Example server certificate has incorrectly formatted subject [Closed]

> Username: utsavm9  
> Created at: 2021-06-25 23:16:19 UTC  
> Updated at: 2022-06-21 23:39:25 UTC  
> Closed at: 2022-06-21 23:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2266  

There is an example of an OpenSSL command here:  
https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/example/common/server_certificate.hpp#L32-L35  
  
I think the subject field is not set correctly set by that:  
  
- On a recent OpenSSL (LibreSSL 2.8.3 on macOS), using that command outputs an error `Subject Attribute /C has no known NID, skipped` and generates a certificate with an empty subject.  
- Checking the certificate in the `std::string cert` on that code file with: `openssl x509 -in cert.pem -text -noout`, I see `Subject: C=US, ST=CA, L=Los AngelesO=BeastCN=www.example.com`. Here, the common name seems to have become a part of the location.  
  
## Fix  
I think the command and the example certificate in the string needs to be generated using:  
```bash  
openssl req \  
    -newkey rsa:2048 -nodes -keyout key.pem \  
    -x509 -days 10000 -out cert_correct.pem \  
        -subj "/C=US/ST=CA/L=Los Angeles/O=Beast/CN=www.example.com"  
```  
as then the subject field is set as `Subject: C=US, ST=CA, L=Los Angeles, O=Beast, CN=www.example.com`  
See `-subj arg` in https://www.openssl.org/docs/man1.1.1/man1/req.html.  
  
## Why  
For local testing, I just replaced `www.example.com` with `localhost` in the command to test if my server based on https://github.com/boostorg/beast/tree/develop/example/http/server/async-ssl is working or not. But since the common name was not set right, `curl --cacert <file>` for local integration testing would not accept the HTTPS connection to a locally running server. Most resources online on this error point to using `curl --insecure`/`curl -k` to fix this, which is quite misleading.

---

## Comment 1

> Username: utsavm9  
> Created at: 2021-06-25 23:21:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2266#issuecomment-868880399  

Do you think those certificates should be corrected? I can make a PR for that, using latest OpenSSL on Ubuntu.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-06-26 01:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2266#issuecomment-868899718  

Please go ahead and create a PR. Thank you.

---

## Comment 3

> Username: utsavm9  
> Created at: 2021-07-08 23:58:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2266#issuecomment-876816237  

Let me know if #2279 resolves this.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2266#issuecomment-1008220769  

This issue has been open for a while with no activity, has it been resolved?
