# #617 - Error Location while decoding [Closed]

> Username: RoyBellingan  
> Created at: 2021-09-07 11:56:03 UTC  
> Updated at: 2023-01-12 09:47:21 UTC  
> Closed at: 2023-01-12 09:47:21 UTC  
> Url: https://github.com/boostorg/json/issues/617  

I was looking at some example and API, and I really think is not possible to have out of the box the ability to detect at which offset an error happened.  
  
I think this could be "easily" added just doing pointer math using the start of the supplied string to parse and propagating back the end_ inside the basic_parser.  
  
I tried and looks like is working fine.  
  
If there is there interest in adding such capability, I will try to produce a usable patch.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-08 14:04:39 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-915269391  

I'm not sure if I want to mess with the parser. If it is really easy, I wouldn't mind looking at your patch to see what's involved. But I can't guarantee that we will merge it.

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-09-08 16:07:42 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-915372055  

Would this do the trick?  
```c++  
parser p;  
error_code ec;  
std::size_t consumed = 0;  
while (!ec  && has_input()) {  
  consumed += p.write_some(next_input(), ec);  
}  
  
if (ec) {  
  std::cerr << "Error: " << ec << " at " << consumed << '\n';  
} else {  
  value jv = p.release();   
}  
```

---

## Comment 3

> Username: RoyBellingan  
> Created at: 2021-09-08 19:51:15 UTC  
> Updated at: 2021-09-08 19:55:04 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-915524104  

Thank you for the consideration, @grisumbras I think is a nice trick, but will incour in quite a lot of overhead.  
  
If you check you already have almost all that is needed! Is here in https://github.com/boostorg/json/blob/develop/include/boost/json/basic_parser_impl.hpp#L286  
Whenever there is an error the character that caused the error is already saved, only creating a few getter to pass back this end and the basic_parser inside the parser is enought to expose it.  
  
At that point I think is ok to do just subtrace end - jsonRaw.start, et voilà.  
  
final code is something like  
```C++  
parser p(string, ec , ... whatever);  
....  
if (ec) {  
   auto pos =  p.getLastParsedChar() - string.c_str();  
}  
...  
const char * getLastParsedChar(){  
 return getInnserParser().getEnd();  
}  
```  
Of course I will add the small algo to compute the number of newline (if any) to have an even better error result.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-09-08 19:54:16 UTC  
> Updated at: 2021-09-08 19:54:31 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-915526039  

> At that point I think is ok to do just subtrace end - jsonRaw.start, et voilà.  
  
Except that in the streaming case, we don't have "jsonRaw.start." To do this correctly, the parser needs to sum the total number of characters previously passed into it, and add the number of characters parsed in the current buffer up until the error.  
  
You said that the suggestion above is a nice trick but will incur overhead. What overhead are you talking about? I don't see any overhead.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-09-08 19:55:13 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-915526637  

> final code is something like  
  
This won't work in the streaming case

---

## Comment 6

> Username: RoyBellingan  
> Created at: 2021-09-08 19:57:32 UTC  
> Updated at: 2021-09-08 20:00:45 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-915528054  

Uhm in case of the streaming yes is quite more complex, but in the streaming case you will only have the offset of the last block parsed.   
  
  
I will check if  calling continuosly the p.write_some add overhead, but maybe this is already what is done internally, I will give a look about that.  
  
But yes each time I see @grisumbras idea I think is better...!

---

## Comment 7

> Username: RoyBellingan  
> Created at: 2021-09-11 12:59:57 UTC  
> Updated at: 2021-09-11 13:02:55 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-917403689  

So I finally had a bit of time and did a small bench and sorry the proposed solution as feared is  ~30x slower...  
  
  
Parsing https://raw.githubusercontent.com/RichardHightower/json-parsers-benchmark/master/data/citm_catalog.json  
The result are   
  
Compiled as -march=native -O3 on intel i7-9750H  
Simple Method Time:                  3816098    
Position detector Time:           125173830   
  
More detail in https://gist.github.com/RoyBellingan/be86169547fd1671139a8ed2606c7010  
  
I hope I am not doing any error!  
  
Quick edit, adding a monotonic_resource lead to even stronger delta  
  
Simple Method Time:                 2284407    
Position detector Time:           110135344   
  
Interesting to note both of them decreased by the same amount

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-09-11 13:02:09 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-917404031  

> I hope I am not doing any error!  
  
Why are you writing 1 character at a time?

---

## Comment 9

> Username: RoyBellingan  
> Created at: 2021-09-11 13:04:03 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-917404324  

To find at which character the error happened, and is what I think   
consumed += p.write_some(next_input(), ec);   
Is supposed to do.  
I will try now a bigger batch, even if the error position is approximated is still fine.  
I will now try some batch size

---

## Comment 10

> Username: vinniefalco  
> Created at: 2021-09-11 13:04:46 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-917404470  

Just write `raw` in one call...

---

## Comment 11

> Username: RoyBellingan  
> Created at: 2021-09-11 13:07:30 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-917404869  

Ok I now understood, Sorry I totally missed the whole point of the streaming parser -.-

---

## Comment 12

> Username: RoyBellingan  
> Created at: 2021-09-11 13:10:13 UTC  
> Url: https://github.com/boostorg/json/issues/617#issuecomment-917405262  

Ok   
		monotonic_resource mr;  
		stream_parser      p(&mr);  
		auto start = chrono::steady_clock::now();  
		int pos = p.write(raw, ec);  
		auto end   = chrono::steady_clock::now();  
		  
Is perfect, and more than excellent for any real case   
  
Simple Method Time:                 1682723    
Position detector Time:             3442505
