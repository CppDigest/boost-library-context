# #2910 - Add `Using Certificates` to Documentation [Closed]

> Username: ashtum  
> Created at: 2024-07-29 14:13:17 UTC  
> Updated at: 2025-02-26 15:01:18 UTC  
> Closed at: 2025-02-26 15:01:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2910  

We need to provide an explanation on using certificates and certificate authorities for client and server roles. Additionally, we should include a subsection that addresses common pitfalls and troubleshooting.

---

## Comment 1

> Username: Zen0x7  
> Created at: 2024-08-10 05:44:12 UTC  
> Updated at: 2024-08-11 04:04:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2910#issuecomment-2279468184  

I'll leave this as a suggested input resource:  
  
In order to use SSL on TCP communications then you should use a SSL Context like the following code:   
  
```cpp  
boost::asio::ssl::context ssl_context{boost::asio::ssl::context::tlsv12};  
ssl_context.set_options(boost::asio::ssl::context::default_workarounds | boost::asio::ssl::context::single_dh_use);  
ssl_context.use_certificate_chain_file("certificates/public.pem");  
ssl_context.use_private_key_file("certificates/private.key", boost::asio::ssl::context::pem);  
ssl_context.use_tmp_dh_file("certificates/dh-params.pem");  
```  
  
If you don't know how to how to get the public and private keys, you could use `OpenSSL` to generate them:  
  
```  
openssl genpkey -algorithm RSA -out private.key  
openssl req -newkey rsa:2048 -key private.key -nodes -out server.csr -subj "/CN=*.company.com"  
openssl x509 -req -in server.csr -signkey private.key -out public.pem -days 365000  
openssl dhparam -out dh-params.pem 2048  
```  
  
Of course, those certificates will be not trusted by in-market browsers as they're not signed by a trusted authority.  
  
If you're interested about how to use those certificates in the client, you could generate another public certificate using the third line of previous command, to get and use it on the clients context.  
  
> IMHO, when you're using those certificates in IoT implementations, use the highest quantity of days as possible, as expired certificates can raise handshake exceptions.  
  
Be aware that using SSL will increase the CPU usage and network bandwidth in a directly proportional way to the encryption size (see [this tool](https://www.devglan.com/online-tools/rsa-encryption-decryption) and compare the base64 result of "abc" being encrypted with 2048 bit key).
