# #2015 - Getting "partial message" error on http_client_async [Closed]

> Username: esamson33  
> Created at: 2020-07-14 13:53:31 UTC  
> Updated at: 2022-06-07 16:22:45 UTC  
> Closed at: 2022-06-07 15:12:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2015  

Hi,  
  
I modified the example code in [http_client_async](https://www.boost.org/doc/libs/1_73_0/libs/beast/example/http/client/async/http_client_async.cpp) to create multiple sessions and download multiple files.  
  
So on main(), I did something like:  
```  
// Launch the asynchronous operation  
for (int n=0; n<10; ++n)  
{  
  std::ostringstream os;  
  os << target  
     << "test"  
     << n+1  
     << "_20M.csv";  
  std::make_shared<session>(ioc)->run(host, port, os.str().c_str(), version);  
}  
```  
Each one of the sessions then start to download "some_target/testN_20M.csv". However, some, if not all, of the sessions end with an error: "read: partial message". Hope you can help.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-14 14:00:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658197626  

Hi @esamson33 , can you link to a github repo containing the complete program you have modified, including build instructions?  
  
Just to be sure we're on the same page.

---

## Comment 2

> Username: esamson33  
> Created at: 2020-07-14 23:22:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658460136  

@madmongo1 please see this [link](https://github.com/esamson33/http_client_async)

---

## Comment 3

> Username: esamson33  
> Created at: 2020-07-15 00:31:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658478937  

I suspected that the timeout set before the last operation was still effective even on subsequent operations. I tried tcp_stream::expires_never() prior to reading the header and the partial message error disappears.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-07-15 00:32:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658479247  

If there was a timeout you should get `beast::error::timeout` not `partial_message`

---

## Comment 5

> Username: esamson33  
> Created at: 2020-07-15 00:49:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658483308  

I reduced the timeout to 10 secs to make sure that it gets hit. Without tcp_stream::expires_never() I always get partial_message and no beast::error::timeout.  
  
A sample run below:  
```  
96881,30001,32660,91145,72555,1591,34374,7682,25read body: read bodypartial message  
: partial message  
read body: partial message  
read body: partial message  
read body: partial message  
read body: partial message  
read body: partial message  
  
real	0m10.049s  
user	0m3.189s  
sys	0m2.615s  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-07-15 01:00:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658485830  

@madmongo1 if correct, this is a bug

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-07-15 14:37:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658807168  

👀

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-07-15 15:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658835631  

Unable to replicate:  
https://github.com/test-scenarios/http_client_async  
  
command line used:  
```  
$ ./http_client_async localhost 8000 / 2>&1 | tr -d 'aa'  
```  
  
No error output produced.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-07-15 15:33:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-658837833  

Why do you get this output: `96881,30001,32660,91145,72555,1591,34374,7682` while I get the content of the test files?

---

## Comment 10

> Username: esamson33  
> Created at: 2020-07-15 22:34:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659049327  

@madmongo1 yes that's from the sample data

---

## Comment 11

> Username: esamson33  
> Created at: 2020-07-15 23:05:27 UTC  
> Updated at: 2020-07-15 23:05:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659059258  

@madmongo1 using the server included I needed to reduce the timeout to 1sec to make sure it kicks in and also comment out tcp_stream::expires_never() to bring out at least a few partial message errors.  
  
PR created.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-07-16 12:15:05 UTC  
> Updated at: 2020-07-16 12:15:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659371816  

@esamson33   
  
OK, it's starting to make sense now.  
  
The timeout represents the maximum time allowed to send the request and receive the complete response. I.e. each intermediate async_read_some does not reset the timer. The larger the payload and slower the network, the more likely a timeout will occur.  
  
I would say that the error you are seeing is because the timeout is interrupting the read.  
  
Vinnie and I have discussed a different kind of basic_stream, in which a timeout can be applied to _each_ underlying async_read_some. i.e. it would detect an unresponsive peer rather than representing an overall time cap on receiving the document.  
  
Is that more what you are looking for?

---

## Comment 13

> Username: esamson33  
> Created at: 2020-07-16 13:47:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659424148  

\> I would say that the error you are seeing is because the timeout is interrupting the read.  
Right. And the way I addressed is to reset the timeout using tcp_stream::expires_never().  
  
Running \$ ./build/http_client_async localhost 8000 / 2>&1 | grep -E "partial|timeout" | tr -d 'aa', I get  
```  
red body: prtil messge  
red heder: The socket ws closed due to  timeout  
red heder: The socket ws closed due to  timeout  
red heder: The socket ws closed due to  timeout  
red heder: The socket ws closed due to  timeout  
red heder: The socket ws closed due to  timeout  
red heder: The socket ws closed due to  timeout  
red heder: red bodyred heder: The socket ws closed due to  timeout  
The socket ws closed due to  timeout  
: prtil messge  
```  
where 2 of 10 are "partial message".  
  
On the chance that the reading of the responses reach the body and the timeout kicks in, the error becomes "partial message" instead of the more obvious "timeout". Still makes sense though but not obvious.  
  
With a 30 secs timeout, the files hosted on a network, and with sizes of 2G each, I see errors to be all partial message rather than timeout.

---

## Comment 14

> Username: madmongo1  
> Created at: 2020-07-16 14:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659437017  

Understood.  
  
The error is converted here:  
https://github.com/boostorg/beast/blob/0b68ed651b6bc7b681cf440ed6a220089e21473f/include/boost/beast/http/impl/read.hpp#L57  
  
EOF is left unchanged but any other error is converted to partial_message.  
  
@vinniefalco Was this the intent?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-07-16 14:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659437019  

> I would say that the error you are seeing is because the timeout is interrupting the read.  
  
Then the error should be `error::timeout` not `partial_message`. The `partial_message` is only returned when the end-of-file is reached on the stream, clearly not happening here.

---

## Comment 16

> Username: madmongo1  
> Created at: 2020-07-16 15:36:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-659493140  

@esamson33   
  
OK, we have agreement that the behaviour you're seeing is a bug.  
  
I'll need to think about what the correct behaviour should be.

---

## Comment 17

> Username: stale[bot]  
> Created at: 2020-08-16 07:19:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-674490933  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: madmongo1  
> Created at: 2020-08-16 08:23:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-674497337  

ping

---

## Comment 19

> Username: cyrillicw  
> Created at: 2022-06-07 14:45:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-1148774924  

Has the problem been resolved?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2022-06-07 15:12:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-1148808243  

> Has the problem been resolved?  
  
There is no problem. The user's issue is that the timeout is kicking in. Either increase the timeout, or disable it entirely.

---

## Comment 21

> Username: cyrillicw  
> Created at: 2022-06-07 15:16:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-1148812909  

@vinniefalco but is there a possibility to set timeout on an individual async_read_some call?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2022-06-07 16:09:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-1148875162  

Not through beast, but you could do that yourself by declaring your own timer and setting it.

---

## Comment 23

> Username: cyrillicw  
> Created at: 2022-06-07 16:22:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2015#issuecomment-1148891098  

Got it. Thank you!
