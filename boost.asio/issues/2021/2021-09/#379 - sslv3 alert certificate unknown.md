# #379 - sslv3 alert certificate unknown [Open]

> Username: runner365  
> Created at: 2021-09-14 10:39:46 UTC  
> Updated at: 2023-01-16 01:52:56 UTC  
> Url: https://github.com/boostorg/asio/issues/379  

The way to make key file and crt file:  
<pre>  
#generate ca key  
openssl genrsa -out ca.key 2048  
#generate ca crt  
openssl req -new -x509 -days 36500 -key ca.key -out ca.crt  
  
################################################  
#generate server private key  
openssl genrsa -out server.key 2048  
  
openssl req -new -key server.key -out server.csr  
  
openssl x509 -req -days 3650 -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt  
</pre>  
  
the server code:  
<pre>  
std::string cert_file = "server.crt";  
std::string key_file  = "server.key";  
boost::asio::ssl::context  ssl_ctx_(boost::asio::ssl::context::tlsv12);  
ssl_ctx_.use_certificate_file(cert_file, boost::asio::ssl::context::pem);  
ssl_ctx_.use_private_key_file(key_file, boost::asio::ssl::context::pem);  
</pre>  
  
the web js code:  
<pre>  
                let url = "wss://172.29.78.76:4443";  
                console.log("websocket url:", url);  
                try {  
                    let wsConn = new WebSocket(url);  
                } catch (error) {  
                    console.log("websocket exception:", error);  
                }  
</pre>  
  
but server report error:  
sslv3 alert certificate unknown。  
  
please help.

---

## Comment 1

> Username: bakercp  
> Created at: 2023-01-12 22:06:30 UTC  
> Url: https://github.com/boostorg/asio/issues/379#issuecomment-1381045671  

@runner365  -- this is an old issue, but I ran across the same thing today.  How did you end up resolving it?

---

## Comment 2

> Username: runner365  
> Created at: 2023-01-16 01:52:56 UTC  
> Url: https://github.com/boostorg/asio/issues/379#issuecomment-1383348216  

> @runner365 -- this is an old issue, but I ran across the same thing today. How did you end up resolving it?  
  
not yet. But I write websocket by myself from zero and I give up boost.asio which is too heavy.
