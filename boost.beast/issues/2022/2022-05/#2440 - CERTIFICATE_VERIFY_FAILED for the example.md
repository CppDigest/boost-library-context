# #2440 - CERTIFICATE_VERIFY_FAILED for the example [Closed]

> Username: clay4megtr  
> Created at: 2022-05-26 18:07:23 UTC  
> Updated at: 2022-05-27 12:12:17 UTC  
> Closed at: 2022-05-27 12:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2440  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
 1_76  
  
### Steps necessary to reproduce the problem  
I got error msg `CERTIFICATE_VERIFY_FAILED` in client and `TLSV1_ALERT_UNKNOWN_CA` in server when I try to run the `http_client_sync_ssl.cpp` and `http_server_sync_ssl.cpp`, Does anyone know somehting about this?    
![image](https://user-images.githubusercontent.com/17047853/170548448-3b57052d-f988-4ad8-8015-03dd6a146787.png)  
  
![image](https://user-images.githubusercontent.com/17047853/170548489-a6688470-2988-4b49-a1d6-e5ec7a85c48e.png)  
  
by the way,  I can only compile my project after I revert the commit `5631d594b3d58344221625b3a9dd022d561664ed`, I'm not sure if it matters.   
  
### All relevant compiler information

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-26 21:34:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2440#issuecomment-1139082277  

You're testing against `0.0.0.0`?The best you can hope for is for that to connect to localhost: https://unix.stackexchange.com/a/419881/5946  
  
The error message then means that your server uses a certificate that cannot be verified. Yes, people know about this. This is very common when servers use self-signed certificates. Add the root to your CA path (see e.g. https://www.boost.org/doc/libs/1_79_0/doc/html/boost_asio/reference/ssl__context/add_verify_path.html) or use a "proper" certificate, e.g. [Let's Encrypt](https://letsencrypt.org/)  
  
With `http_server_sync_ssl.cpp` it's the other way around. You probably connect using a  browser and *it* rejects the self-signed certificate used by the examples. The certificate can be found in the source code https://www.boost.org/doc/libs/1_79_0/libs/beast/example/common/server_certificate.hpp.  
  
Either trust the certificate, by-pass the verification in your browser, or use a "proper" certicate again.

---

## Comment 2

> Username: clay4megtr  
> Created at: 2022-05-27 09:29:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2440#issuecomment-1139446643  

> You're testing against `0.0.0.0`?The best you can hope for is for that to connect to localhost: https://unix.stackexchange.com/a/419881/5946  
>   
> The error message then means that your server uses a certificate that cannot be verified. Yes, people know about this. This is very common when servers use self-signed certificates. Add the root to your CA path (see e.g. https://www.boost.org/doc/libs/1_79_0/doc/html/boost_asio/reference/ssl__context/add_verify_path.html) or use a "proper" certificate, e.g. [Let's Encrypt](https://letsencrypt.org/)  
>   
> With `http_server_sync_ssl.cpp` it's the other way around. You probably connect using a browser and _it_ rejects the self-signed certificate used by the examples. The certificate can be found in the source code https://www.boost.org/doc/libs/1_79_0/libs/beast/example/common/server_certificate.hpp.  
>   
> Either trust the certificate, by-pass the verification in your browser, or use a "proper" certicate again.  
  
I get it! Thx for the fast reply! very appreciate it.
