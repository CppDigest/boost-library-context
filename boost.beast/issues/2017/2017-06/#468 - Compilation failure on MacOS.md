# #468 - Compilation failure on MacOS [Closed]

> Username: rberlich  
> Created at: 2017-06-11 17:04:52 UTC  
> Updated at: 2017-06-12 02:47:42 UTC  
> Closed at: 2017-06-12 02:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/468  

Hi there,  
  
I am getting a compilation failure for the current Beast revision on a Mac (Sierra, 10.12.5, all updates, including a recent Xcode update):  
  
```  
Beast-master/include/beast/http/impl/read.ipp:280:9: error:   
      fallthrough annotation does not directly precede switch label  
        BOOST_FALLTHROUGH;  
```  
  
The code in question is   
```  
void  
read_op<Stream, DynamicBuffer,  
    isRequest, Derived, Condition, Handler>::  
operator()(error_code ec)  
{  
    switch(state_)  
    {  
    case 0:  
        if(Condition{}(p_))  
        {  
            state_ = 1;  
            return s_.get_io_service().post(  
                bind_handler(std::move(*this), ec));  
        }  
        state_ = 2;  
        BOOST_FALLTHROUGH;  
  
    do_read:  
        return async_read_some(  
            s_, b_, p_, std::move(*this));  
  
    case 1:  
        goto upcall;  
  
    case 2:  
    case 3:  
        if(ec)  
            goto upcall;  
        if(Condition{}(p_))  
            goto upcall;  
        state_ = 3;  
        goto do_read;  
    }  
upcall:  
    h_(ec);  
}  
```  
  
Removing the `BOOST_FALLTHROUGH` (which seems to be a clang-specific optimization) eliminates the problem (fallthrough should happen anyway, albeit possibly at the cost of a small loss of performance on clang.  
  
Alternatively, for the above function, it should be possible to move `do_read:`right before `upcall:`.  There is one other occurance of BOOST_FALLTHROUGH` for the same file, however, for which this will be more difficult.  
  
Personally I would prefer not mixing case-labels with more "traditional" jump targets.  
  
### Beast Version:   
53 (from version.hpp) / Download on June 11  
### Platform:   
```  
clang -v  
Apple LLVM version 8.1.0 (clang-802.0.42)  
Target: x86_64-apple-darwin16.6.0  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-11 20:16:58 UTC  
> Updated at: 2017-06-11 20:17:51 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307653956  

You're suggesting  
```  
void  
read_op<Stream, DynamicBuffer,  
    isRequest, Derived, Condition, Handler>::  
operator()(error_code ec)  
{  
    switch(state_)  
    {  
    case 0:  
        if(Condition{}(p_))  
        {  
            state_ = 1;  
            return s_.get_io_service().post(  
                bind_handler(std::move(*this), ec));  
        }  
        state_ = 2;  
        break;  
  
    case 1:  
        goto upcall;  
  
    case 2:  
    case 3:  
        if(ec)  
            goto upcall;  
        if(Condition{}(p_))  
            goto upcall;  
        state_ = 3;  
        break;  
    }  
    return async_read_some(  
        s_, b_, p_, std::move(*this));  
upcall:  
    h_(ec);  
}  
```  
?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-11 20:21:00 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307654179  

Is it related to this issue? https://bugs.llvm.org/show_bug.cgi?id=17864  
  
Do you have your compiler set to turn warnings into errors?

---

## Comment 3

> Username: rberlich  
> Created at: 2017-06-11 20:37:59 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307655186  

No, I did not turn warnings into errors, and there are indeed some warnings when compiling Beast on Mac (related to some ASIO-code, which I believe is a known issue, so I tend to ignore them). These do not result in a compilation failure.  
  
Since I just (yesterday, IIRC) updated Xcode, the local Clang should be up-to-date and I doubt that the problem is related to [https://bugs.llvm.org/show_bug.cgi?id=17864](url) -- that was reported in 2013, after all. I just believe Clang does not accept the `do_read:` as a valid label in a switch statement.   
  
As for a possible fix, your suggestion would probably work for this function. I had however thought of the more verbatim  
  
```  
void  
read_op<Stream, DynamicBuffer,  
    isRequest, Derived, Condition, Handler>::  
operator()(error_code ec)  
{  
    switch(state_)  
    {  
    case 0:  
        if(Condition{}(p_))  
        {  
            state_ = 1;  
            return s_.get_io_service().post(  
                bind_handler(std::move(*this), ec));  
        }  
        state_ = 2;  
        goto do_read;  
  
    case 1:  
        goto upcall;  
  
    case 2:  
    case 3:  
        if(ec)  
            goto upcall;  
        if(Condition{}(p_))  
            goto upcall;  
        state_ = 3;  
        goto do_read;  
    }  
  
do_read:  
     return async_read_some(s_, b_, p_, std::move(*this));  
  
upcall:  
    h_(ec);  
}  
```  
(note: untested!)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-11 20:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307655381  

Is that the only error? Because there are quite a few places in Beast that mix gotos and case labels, with `BOOST_FALLTHROUGH` mixed in there. For example here: https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/serializer.ipp#L93  
  
I'm trying to figure out the best way to fix this. I'm hesitant to change the structure of the switch, even with the labels and the odd gotos, because they are very fiddly to get working. I write them using a set of rules that makes them easier to audit in code reviews. For example the upcall always happens at the end of the function. And a `return` always implies a continuation of asynchronous control flow.

---

## Comment 5

> Username: rberlich  
> Created at: 2017-06-11 20:49:51 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307655928  

I had to remove `BOOST_FALLTHROUGH` in both locations in `Beast-master/include/beast/http/impl/read.ipp` to get it working, but in NO other file. Looking at [https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/serializer.ipp#L93](url) , one difference seems to be the curly braces around the "case-code". I'll just test if this makes a difference, and if you send some other possibility in the next 1-2 hours, I'd be happy to test that too (I'm in German time, so it is close to 11 pm here). After that, I'd only be able to test a day later, due to some competing work.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-11 20:51:06 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307656003  

Oh...I see the problem!!! Those fallthroughs simply should not be there... I got confused and thought `do_read` was a case label. You're right, its confusing, even to me! I will put a fix in **v54** right away. Sorry about that!  
  
`[[fallthrough]]` has to appear *immediately* before a **case** label.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-11 20:53:22 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307656113  

This should resolve your issue, thanks! https://github.com/vinniefalco/Beast/pull/465

---

## Comment 8

> Username: rberlich  
> Created at: 2017-06-11 21:19:26 UTC  
> Updated at: 2017-06-11 21:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/468#issuecomment-307657564  

I can confirm that the v53 code with the v54 fix for read.ipp compiles on my Mac -- thanks a lot for the quick fix!
