# #353 - asio tls server bug [Closed]

> Username: midle110  
> Created at: 2020-05-22 04:50:05 UTC  
> Updated at: 2020-05-22 14:58:26 UTC  
> Closed at: 2020-05-22 14:58:26 UTC  
> Url: https://github.com/boostorg/asio/issues/353  

server listen 8888 port and use www.a.com cert  
  
demo like example https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/example/cpp11/ssl/server.cpp  
  
shell code  
curl -v https://127.0.0.1:8888  
  
```  
root@bogon:/home/pi# curl -v https://127.0.0.1:8888  
* Rebuilt URL to: https://127.0.0.1:8888/  
*   Trying 127.0.0.1...  
* TCP_NODELAY set  
* Connected to 127.0.0.1 (127.0.0.1) port 8888 (#0)  
* ALPN, offering h2  
* ALPN, offering http/1.1  
* Cipher selection: ALL:!EXPORT:!EXPORT40:!EXPORT56:!aNULL:!LOW:!RC4:@STRENGTH  
* successfully set certificate verify locations:  
*   CAfile: /etc/ssl/certs/ca-certificates.crt  
  CApath: /etc/ssl/certs  
* TLSv1.2 (OUT), TLS header, Certificate Status (22):  
* TLSv1.2 (OUT), TLS handshake, Client hello (1):  
* TLSv1.2 (IN), TLS handshake, Server hello (2):  
* TLSv1.2 (IN), TLS handshake, Certificate (11):  
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):  
* TLSv1.2 (IN), TLS handshake, Server finished (14):  
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):  
* TLSv1.2 (OUT), TLS change cipher, Client hello (1):  
* TLSv1.2 (OUT), TLS handshake, Finished (20):  
* TLSv1.2 (IN), TLS change cipher, Client hello (1):  
* TLSv1.2 (IN), TLS handshake, Finished (20):  
* SSL connection using TLSv1.2 / ECDHE-RSA-AES256-GCM-SHA384  
* ALPN, server did not agree to a protocol  
* Server certificate:  
*  subject: CN=www.A.com  
*  start date: May 12 06:27:24 2020 GMT  
*  expire date: Aug 10 06:27:24 2020 GMT  
*  subjectAltName does not match 127.0.0.1  
* SSL: no alternative certificate subject name matches target host name '127.0.0.1'  
* Curl_http_done: called premature == 1  
* stopped the pause stream!  
* Closing connection 0  
* TLSv1.2 (OUT), TLS alert, Client hello (1):  
curl: (51) SSL: no alternative certificate subject name matches target host name '127.0.0.1'  
```  
  
then server accept client ,and HandShack successed.  
loop DoRead.  
  
void CConnectSSL::DoRead()  
{  
	auto self = shared_from_this();  
  
	m_ssl_socket.async_read_some(boost::asio::buffer(m_recv_data, RECV_BUFF_LEN),  
		bind(&CConnect::ReadCb, self, placeholders::_1, placeholders::_2));  
}  
  
because ReadCb fcuntion  length  == 0

---

## Comment 1

> Username: midle110  
> Created at: 2020-05-22 14:58:26 UTC  
> Url: https://github.com/boostorg/asio/issues/353#issuecomment-632737465  

sorry ,it's my bug
