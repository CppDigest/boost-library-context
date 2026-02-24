# #162 - Handler not invoked correctly in multi-byte UTF8 sequences [Closed]

> Username: rtobar  
> Created at: 2020-08-17 08:51:06 UTC  
> Updated at: 2020-08-22 14:17:37 UTC  
> Closed at: 2020-08-22 14:17:37 UTC  
> Url: https://github.com/boostorg/json/issues/162  

Now that number literals are implemented, I gave this library a second test round. However I found a new problem (that I previously didn't experience) that prevented me from advancing further.  
  
The problem happens when one feeds multi-byte UTF8 sequences to a `basic_parser` *one char at a time*. In such situations `on_string_part` is called each time a sequence finishes, but its `string_view` parameter contains only the last byte of the sequence.  
  
I think this is better illustrated with a test, so I implemented one. See https://github.com/rtobar/json/commit/fde197b27006f7d6917b40b77df3cdd96bdb648c for a test that reproduces this problem. I'm 90% sure I'm doing things correctly, but please indicate if usage is not as intended.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-17 09:14:14 UTC  
> Url: https://github.com/boostorg/json/issues/162#issuecomment-674762671  

@rtobar Gah, I knew I forgot to get around to fixing this. I can get you a hot fix for now and I'll work on optimizing it tomorrow.

---

## Comment 2

> Username: rtobar  
> Created at: 2020-08-17 09:15:34 UTC  
> Url: https://github.com/boostorg/json/issues/162#issuecomment-674763357  

I'm not in a rush, so don't worry about a hotfix. I just wanted to raise the issue, maybe also get the test added into the test suite for completeness.

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-08-17 09:41:51 UTC  
> Updated at: 2020-08-17 09:48:20 UTC  
> Url: https://github.com/boostorg/json/issues/162#issuecomment-674776912  

Alright, in that case we can incorporate the test you provided and I'll get this fixed tomorrow.   
  
@vinniefalco I think what I'm going to do is when the string is unescaped, we will directly call the handler with the byte sequence from the input. Buffered string parsing is harder, so maybe we just don't reclip the stream and save the byte sequence in a member?
