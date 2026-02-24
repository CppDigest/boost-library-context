# #2223 - compiler error on dynamic_vector_buffer [Closed]

> Username: gopalak  
> Created at: 2021-05-03 17:11:34 UTC  
> Updated at: 2021-05-06 11:23:32 UTC  
> Closed at: 2021-05-06 11:23:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2223  

I am trying to yse a dynamic_vector_buffer with an underlying uint8_t vector. (This is a class data member). But when I declare the dynamic_vector_buffer I see the following compiler error.  
  
asio::dynamic_vector_buffer   m_in_db = asio::dynamic_buffer(_in_buf);  
and my _in_buf is of type vector<uint8_t>  
  
--- Error I see ---  
  
srvr.cpp:89:35: error: missing template arguments before ‘m_in_db’  
     asio::dynamic_vector_buffer   m_in_db =  
  
Also I am not sure on using boost::optional to declare the dynamic_vector_buffer as a data member.  
Appreciate the help note @vinniefalco

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-03 17:25:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831409519  

This is an asio question.  
  
The error message is telling you that `dynamic_vector_buffer` is a template class, and you have not provided template arguments.  
  
Let's wait until we have solved that, before we talk about the boost::optional question.

---

## Comment 2

> Username: gopalak  
> Created at: 2021-05-03 17:34:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831414829  

Got it. I did fix  
asio::dynamic_vector_buffer<uint8_t, std::allocator<uint8_t>>   m_in_db =  
    asio::dynamic_buffer(_in_buf);  
  
I would like to have this m_in_db as a data member as I have my handler a bit involved and not easy to put as a lambda

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-03 17:38:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831416816  

You'll probably find that you can leave out the `, std::allocator<uint8_t>` part as this is the default.  
  
The asio dynamic buffers are designed to be lightweight, copyable references to an underlying data buffer (e.g. `_in_buf`).  
  
You can create the dynamic_buffer when you need it. You don't need to store it anywhere. The operation that uses it will dynamically update the underlying vector `_in_buf`.

---

## Comment 4

> Username: gopalak  
> Created at: 2021-05-03 17:40:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831418212  

Ahh so if I understand it right - just do an asio::buffer(_in_buf) when I call async_read or async_write ? and the _in_buf will be automatically used as its the underlying storage ?

---

## Comment 5

> Username: gopalak  
> Created at: 2021-05-03 17:43:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831419833  

> You'll probably find that you can leave out the `, std::allocator<uint8_t>` part as this is the default.  
>   
> The asio dynamic buffers are designed to be lightweight, copyable references to an underlying data buffer (e.g. `_in_buf`).  
>   
> You can create the dynamic_buffer when you need it. You don't need to store it anywhere. The operation that uses it will dynamically update the underlying vector `_in_buf`.  
  
I did leave it and it complained about the template class needing 2 params.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-03 17:58:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831427897  

> Ahh so if I understand it right - just do an asio::buffer(_in_buf) when I call async_read or async_write ? and the _in_buf will be automatically used as its the underlying storage ?  
  
Yes, but only for ASIO async operations. Beast async operations require that you use the Beast dynamic buffers, which are different, and passed by reference.  
  
> I did leave it and it complained about the template class needing 2 params.  
  
My mistake.

---

## Comment 7

> Username: gopalak  
> Created at: 2021-05-03 18:32:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831448234  

> > Ahh so if I understand it right - just do an asio::buffer(_in_buf) when I call async_read or async_write ? and the _in_buf will be automatically used as its the underlying storage ?  
>   
> Yes, but only for ASIO async operations. Beast async operations require that you use the Beast dynamic buffers, which are different, and passed by reference.  
You mean some thing like beast::multi_buffer ? I really need an under lying uint8_t based storage container in the buffers.  
and the dynamic_vector_buffer seems to be a good fit. Doesnt that gel well with beast websocket stream async_read and write calls ?  
  
>   
> > I did leave it and it complained about the template class needing 2 params.  
>   
> My mistake.

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-05-04 07:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-831747898  

Can we consider this issue closed?

---

## Comment 9

> Username: gopalak  
> Created at: 2021-05-04 16:58:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-832094968  

Yes thanks for the help. By the way where can I post questions on ssl::context specifically on set_password_callback for private key files ?

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-05-04 18:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2223#issuecomment-832142349  

ssl::context is a thin wrapper around OpenSSL.  
  
The OpenSSL documents are a good place to start, and stackoverflow has a good deal on inormation.  
  
You can also join the CppLang slack and see who's online to help you, start in the #beast channel where I live.  
  
https://cppalliance.org/slack/
