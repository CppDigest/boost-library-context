# #2070 - Error body_limit when body_limit is set to none [Closed]

> Username: ErikvLinstee  
> Created at: 2020-08-27 11:29:46 UTC  
> Updated at: 2020-08-29 09:39:53 UTC  
> Closed at: 2020-08-29 09:39:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2070  

If body_limit is set to none, a request always fails with a body limit exceeded error when a content_length header is evaluated.  
  
In finish_header len_ is checked against body_limit_, even when body_limit is none.  
![image](https://user-images.githubusercontent.com/50829335/91436862-3c5a9180-e869-11ea-982b-e14beae0f1b2.png)

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-27 11:38:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2070#issuecomment-681894309  

Thank you. I've assigned it.

---

## Comment 2

> Username: ErikvLinstee  
> Created at: 2020-08-27 11:47:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2070#issuecomment-681898300  

Below is the second finish_header. I have already added the necessary check.  
  
![image](https://user-images.githubusercontent.com/50829335/91438492-b9870600-e86b-11ea-8ac9-5ec0027af404.png)
