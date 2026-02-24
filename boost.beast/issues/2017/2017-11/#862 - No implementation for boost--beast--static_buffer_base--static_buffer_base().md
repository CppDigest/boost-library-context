# #862 - No implementation for boost::beast::static_buffer_base::static_buffer_base() [Closed]

> Username: ksergey  
> Created at: 2017-11-01 14:59:13 UTC  
> Updated at: 2017-11-04 02:33:38 UTC  
> Closed at: 2017-11-04 02:33:38 UTC  
> Url: https://github.com/boostorg/beast/issues/862  

Beast v124  
  
I try to implement own buffer type based on static_buffer_base.  
But looks like implementation of static_buffer_base constructor is not exists.  
  
Possible fix (=default)  
```c++  
protected:  
    /** Constructor  
  
        The buffer will be in an undefined state. It is necessary  
        for the derived class to call @ref reset in order to  
        initialize the object.  
    */  
    static_buffer_base() = default;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-01 15:24:10 UTC  
> Url: https://github.com/boostorg/beast/issues/862#issuecomment-341139323  

Yes! Thank you, this will be fixed in 133. I would be interested to hear how your derived class turns out!

---

## Comment 2

> Username: ksergey  
> Created at: 2017-11-01 15:44:27 UTC  
> Url: https://github.com/boostorg/beast/issues/862#issuecomment-341145686  

Nothing special  
```c++  
     class flat_buffer final  
         : public boost::beast::static_buffer_base  
     {  
     private:  
         std::unique_ptr< char[] > storage_;  
  
     public:   
         flat_buffer(const flat_buffer&) = delete;  
         flat_buffer& operator=(const flat_buffer&) = delete;  
  
         explicit flat_buffer(std::size_t capacity)  
             : boost::beast::static_buffer_base{0, 0}   
             , storage_(new char[capacity])  
         {  
             reset(storage_.get(), capacity);  
         }   
     };      
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-11-01 15:46:19 UTC  
> Url: https://github.com/boostorg/beast/issues/862#issuecomment-341146283  

So you are building a circular buffer with dynamic allocation?

---

## Comment 4

> Username: ksergey  
> Created at: 2017-11-01 15:56:23 UTC  
> Url: https://github.com/boostorg/beast/issues/862#issuecomment-341149441  

Yes. I can't use `static_buffer<...>`, because buffer size (`capacity` in my code) is configurable value.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-11-01 16:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/862#issuecomment-341160401  

Makes sense. Note that beast has a `flat_buffer`, but it works differently (it is not circular).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-11-04 02:33:38 UTC  
> Url: https://github.com/boostorg/beast/issues/862#issuecomment-341865882  

This should be fixed now, please let me know if it gives you trouble again!
