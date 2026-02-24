# #378 - Boost 1.64 error with OpenSSL 1.1.0 [Open]

> Username: jaskarannagi19  
> Created at: 2021-06-23 18:42:44 UTC  
> Updated at: 2021-06-23 22:08:08 UTC  
> Url: https://github.com/boostorg/asio/issues/378  

undefined reference to `EVP_CIPHER_CTX_reset'  
undefined reference to `TLS_method'  
undefined reference to `TLS_client_method'  
undefined reference to `SSL_CTX_get_default_passwd_cb_userdata'  
undefined reference to `SSL_CTX_set_options'  
undefined reference to `TLS_server_method'  
  
I just upgrade the boost to 1.64 for some reason that is the max boost version we can use in current situation.  I have also install openssl 1.1.0 but when compiling the code. I get above errors from boost context.hpp  
  
Please help!!
