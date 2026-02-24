# #1403 - "Beast Test CA" [Closed]

> Username: mahendra-kumar  
> Created at: 2019-01-05 13:13:41 UTC  
> Updated at: 2021-09-02 09:20:35 UTC  
> Closed at: 2019-02-09 09:21:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1403  

### Version: 201  
  
### Steps necessary to reproduce the problem  
  
1) Build and run /example/websocket/server/async-ssl  
2) Try to connect from a wss client like socat  
  
  
### All relevant compiler information  
  
As per the header comment in libs/beast/example/common/server_certificate.hpp used to build this sample:  
`For this to work with the browser or operating system, it is  
    necessary to import the "Beast Test CA" certificate into  
    the local certificate store, browser, or operating system  
    depending on your environment Please see the documentation  
    accompanying the Beast certificate for more details.`  
  
But there is no information on where to find this  "Beast Test CA" and how to import it to make the sample work?  
Any help is appreciated!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-05 15:41:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1403#issuecomment-451665680  

Well, client/server certificates are pretty far out-of-scope for Beast. I include them as an example just to give people a starting point. I actually don't know much about how these certificates work at all, except that if your server has a custom server certificate that is not signed by a CA, and you want your browser to recognize it, then you need to "install" the server certificate into your local store. That's about it, the rest you will have to find out online or elsewhere.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-02-04 22:02:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1403#issuecomment-460430880  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: KerouichaReda  
> Created at: 2021-09-02 09:20:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1403#issuecomment-911441168  

you can generate your own key and certificate by using openssl comand line as follow   
```  
openssl genrsa -des3 -passout pass:xxxx -out server.pass.key 2048  
openssl rsa -passin pass:xxxx -in server.pass.key -out server.key  
openssl req -new -key server.key -out server.csr  
openssl dhparam -out dh.pem 2048  
```  
set context option as follow   
```  
ctx.use_certificate_chain_file("server.crt");  
ctx.use_private_key_file("server.key",boost::asio::ssl::context::file_format::pem);  
ctx.use_tmp_dh_file("dh.pem");  
```  
also set the certificate and the key with your http server(I am using live server extention in vs code)  to achieve HTTPS and you can establish  a secure websocket communication between the server and the browser in your local machine
