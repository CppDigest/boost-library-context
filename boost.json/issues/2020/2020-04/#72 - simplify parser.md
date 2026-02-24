# #72 - simplify parser [Closed]

> Username: vinniefalco  
> Created at: 2020-04-03 00:11:41 UTC  
> Updated at: 2020-05-02 01:04:13 UTC  
> Closed at: 2020-05-02 01:04:12 UTC  
> Url: https://github.com/boostorg/json/issues/72  

we dont need all the extra overloads of `finish()`, we can just make `write` work like `finish` and the user can deal with the error if they want to continue processing the extra data.

---

## Comment 1

> Username: viccpp  
> Created at: 2020-04-14 11:23:34 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613384023  

Don't think it is a simplification. User will need to keep an addition state - the last write result.

---

## Comment 2

> Username: viccpp  
> Created at: 2020-04-14 11:27:22 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613385727  

Off topic:  
Why `write()`? `parse()` would be more logical for me. Parser is for reading. `write()` is appropriate for serializer (output)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-14 14:45:54 UTC  
> Updated at: 2020-04-14 14:46:02 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613485984  

> User will need to keep an addition state - the last write result  
  
Users have to observe that anyway, since they need to check the error code. 99% of users don't care about processing additional data which lies past the JSON, so this is certainly a simplification. We still get the same features but with fewer functions. Having the proliferation of functions was confusing.  
  
To be clear consider this input string: `"[ 1, 2, 3 ] #abc"`  
  
There is a JSON followed by something that isn't a JSON. The parser can handle this, it will simply return 12 indicating that 12 characters were consumed, and the error will be set to `error::extra_data`. Almost all users will want to consider this an error, since the input string is not valid JSON. The users which want to process the remaining string (the "#abc") as something other than JSON, can easily do so by checking for the error and then handling the remaining part of the buffer themselves.

---

## Comment 4

> Username: viccpp  
> Created at: 2020-04-14 18:24:19 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613604296  

What about truncated input? Like:  
  
"{ a: ...."  // ok  
" ... [ 1, 2" // ok  
---EOF--- // ??? what we supposed to do here?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-04-14 18:53:10 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613619769  

I don't understand the question here.  
  
"{ a: ..." is not valid JSON, the key needs to be a string (i.e. in double quotes).  
  
If you can write out exactly what is in each buffer, that would be helpful. For example consider this json:  
  
```  
json::value jv = { 1, 2, 3 };  
```  
  
This produces an array which serializes to the string `"[1,2,3]"`.  
  
We can parse this in two buffers thusly:  
  
```  
json::parser p;  
p.write( "[1," );  
assert( ! p.is_complete() );  
p.write( "2,3]" );  
assert( p.is_complete() );  
```  
  
A number however, needs a call to finish:  
  
```  
json::parser p;  
p.write( "1" );  
assert( ! p.is_complete) );  
p.write( "0" );  
assert( ! p.is_complete) );  
p.finish();  
assert( p.is_complete() );  
assert( number_cast<int>(p.release()) == 10 );  
```  
  
If the end of the buffer is indicated (from a call to `finish`) and the resulting JSON is incomplete, then an error is returned:  
  
```  
error_code ec;  
json::parser p;  
p.write( "[" );  
p.finish( ec );  
assert( ec ); // incomplete JSON  
```

---

## Comment 6

> Username: viccpp  
> Created at: 2020-04-14 19:06:53 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613627157  

`...` means some omitted part of valid JSON. OK, full example:  
  
```  
{ "a" : 1,  
```  
```  
"b" : "abc"  
```  
```  
--EOF--  
```  
  
How to correctly process this without `finish()` that you want to kill?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-04-14 19:13:26 UTC  
> Updated at: 2020-04-14 19:13:33 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613630642  

> How to correctly process this without finish() that you want to kill?  
  
I only removed one overload of `finish`, the one which takes the buffer as a parameter. We still have `finish` with no parameters, which is necessary in your example above.

---

## Comment 8

> Username: viccpp  
> Created at: 2020-04-14 19:23:40 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613635477  

Ah... Ok, undestand now.  
  
> we dont need finish()  
  
sounded too radical for me :-)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-04-14 19:25:17 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-613636197  

> sounded too radical for me :-)  
  
Heh, I should have been more clear. Note, that there were previously **eight** member functions (2 each of write_some, write, finish, and finish, one for errors and one for exceptions).

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-05-02 01:04:12 UTC  
> Url: https://github.com/boostorg/json/issues/72#issuecomment-622646112  

This is done
