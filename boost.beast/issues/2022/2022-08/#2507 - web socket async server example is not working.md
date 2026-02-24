# #2507 - web socket async server example is not working [Closed]

> Username: audi0615  
> Created at: 2022-08-24 06:12:57 UTC  
> Updated at: 2022-09-24 05:08:09 UTC  
> Closed at: 2022-09-24 05:08:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2507  

Hi.  
  
I'm trying to test the web socket async server example, but I got an error on "net::dispatch(...." and the error says "no instance overloading....". my boost library version is 1.78.  
  
I just copied and pasted the example and i have no idea how to fix it. Please help me.  
  
```  
    // Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-08-24 09:02:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2507#issuecomment-1225439637  

Did you copy/paste the example exactly, or have you modified it since the copy/paste?  
  
Can you show the error message emitted by the compiler please?

---

## Comment 2

> Username: audi0615  
> Created at: 2022-08-24 09:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2507#issuecomment-1225471948  

dear madmongo1  
  
i just copy/pasted the example and modified nothing.  
please refer to following error message.  
  
![2022-08-24_18-29-52](https://user-images.githubusercontent.com/96812644/186384030-0a28b5ec-3497-4caa-a00c-f2ff7bc9f142.png)

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-08-24 13:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2507#issuecomment-1225743443  

Is this actually failing to compile? The picture is showing an intellisense lookup failure.

---

## Comment 4

> Username: audi0615  
> Created at: 2022-08-25 01:43:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2507#issuecomment-1226674137  

Wow. It compiles without problems. but still i get intellisense error.   
Thank you so much madmongo1.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:32:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2507#issuecomment-1231415408  

Is this still an open issue?
