# #410 - Change websocket stream read signature [Closed]

> Username: vinniefalco  
> Created at: 2017-06-01 17:51:38 UTC  
> Updated at: 2017-06-12 04:01:20 UTC  
> Closed at: 2017-06-12 04:01:20 UTC  
> Url: https://github.com/boostorg/beast/issues/410  

Instead of having `opcode` as an output reference parameter it could be a property of the stream, "last opcode" and accessible by a member function.

---

## Comment 1

> Username: harrishancock  
> Created at: 2017-06-07 20:29:22 UTC  
> Updated at: 2017-06-09 01:05:04 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-306915193  

I'd love this change!  
  
I have a notional **MessageOrientedAsyncStream** concept in my code. Types satisfy the concept requirements by implementing the following unsurprising interface:  
  
```c++  
struct X {  
    template <class DynamicBuffer, class CompletionToken>  
    auto async_read(DynamicBuffer&& b, CompletionToken&& t);  
    // Read one single message into `b`.  
  
    template <class ConstBufferSequence, class CompletionToken>  
    auto async_write(const ConstBufferSequence& b, CompletionToken&& t);  
    // Write `b` as one single message.  
  
    // ...  
};  
```  
  
Currently I need a little adapter class which provides the `opcode` parameter for me to make `beast::websocket::stream` fit the concept. This isn't terribly onerous, but a more generic `async_read()` would be welcome.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-09 00:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307265102  

Hey I just re-read this and I like this idea of **AsyncMsgStream** (see what I did there?).

---

## Comment 3

> Username: harrishancock  
> Created at: 2017-06-09 01:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307267292  

Thanks! That's certainly less of a mouthful. I find the concept useful in my code: the two models of it I have are WebSockets and a homebrew HDLC-like framing wrapper around a `serial_port`. Certainly there are many more.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-09 01:10:31 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307268143  

Bikeshed moment:  
```  
/// Returns `true` if the last message frame indicated a binary opcode  
bool stream::is_binary() const;  
  
/// Returns `true` if the last message frame indicated a text opcode  
bool stream::is_text() const  
{  
  return ! is_binary(); // derp  
}  
```  
  
Note however that I am getting rid of that silly `set_option` and just making them member functions. So `stream::binary(bool)` will change the setting on outgoing messages, and `bool stream::binary() const` will return the setting for outgoing messages.

---

## Comment 5

> Username: harrishancock  
> Created at: 2017-06-09 01:29:41 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307270498  

I think that's a good direction. The difference between `my_stream.is_binary()` and `my_stream.binary()` might be too subtle for some users -- it'd be fine with me, but I know other shed painters will weigh in during the review.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-09 01:34:32 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307271124  

Do you have any alternatives?

---

## Comment 7

> Username: harrishancock  
> Created at: 2017-06-09 01:51:50 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307273500  

Not really. `last_frame_was_binary()`?  
  
```c++  
if (my_stream.opcode() == beast::websocket::opcode::binary) {  
    // ...  
}  
  
if (my_stream.is_binary()) {  
    //  
}  
  
if (my_stream.last_opcode() == beast::websocket::opcode::binary) {  
    // ...  
}  
  
if (my_stream.last_frame_was_binary()) {  
    // ...  
}  
```  
  
Of those, I think `my_stream.is_binary()` reads the nicest.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-09 01:53:24 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307273716  

I'd like to get rid of `opcode` because it exposes protocol internals that the user can't see

---

## Comment 9

> Username: harrishancock  
> Created at: 2017-06-09 01:57:29 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307274268  

I think that's wise. The first time I looked at the different values of the enum as a user, I was a little intimidated. It made using the protocol seem harder than it needed to be.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-09 02:51:33 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307281350  

How about  
```  
bool stream::got_text() const;  
  
bool stream::got_binary() const  
{  
  return ! got_text();  
}  
```

---

## Comment 11

> Username: harrishancock  
> Created at: 2017-06-09 03:01:07 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307282622  

I like it. It's clearer what it means to me than the `is_binary()` style.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-09 03:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307283651  

Synopsis:  
```  
void binary(bool); // set outgoing to binary  
bool binary() const; // `true` if outgoing is binary  
  
void text(bool); // set outgoing to text  
bool text() const; // `true` if outgoing is text  
  
bool got_binary() const; // last incoming was binary  
bool got_text() const; // last incoming was text  
```  
  
This will go into v52

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-09 03:15:17 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307284341  

See #446

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-12 04:01:20 UTC  
> Url: https://github.com/boostorg/beast/issues/410#issuecomment-307685207  

This is done
