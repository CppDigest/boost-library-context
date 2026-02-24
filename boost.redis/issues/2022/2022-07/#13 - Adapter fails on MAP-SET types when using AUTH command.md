# #13 - Adapter fails on MAP/SET types when using AUTH command [Closed]

> Username: Eddie-cz  
> Created at: 2022-07-09 07:26:58 UTC  
> Updated at: 2022-07-10 19:04:01 UTC  
> Closed at: 2022-07-10 19:04:01 UTC  
> Url: https://github.com/boostorg/redis/issues/13  

Redis 7.0.2, server with auth password set.  
Using example https://github.com/mzimbres/aedis/blob/master/examples/containers.cpp,  
I added AUTH command into request queue, expanded resp tuple for AUTH response and adjusted print functions to count with changed tuple indexes.  
When running this adjusted example, aedis fails on "expects map type", when trying to convert hgetall response to std::map. Also fails with set. Vector is fine.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-09 08:27:59 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179503277  

I have updated the containers.cpp example with `AUTH` but I get no problem, see https://github.com/mzimbres/aedis/blob/f8ff3034f4250b5b3fa45d713a79dd2274d61a9e/examples/containers.cpp.  
  
Please, run this example yourself and let me know if it works.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-07-09 09:13:25 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179510948  

Unfortunately not, same output:  
Expects map type.  
1 2 3 4 5 6  
  
I finally nailed it. It works when default user has set nopass. When default user has set password, it doesn't work.  
  
1) "user default on nopass ~* &* +@all"  
2) "user mzimbres on #89acbd96ed4b024bc779d8a4e21d1294881f57a62d3b9d699518590a39e0fbd7 ~* resetchannels +@all"

---

## Comment 3

> Username: mzimbres  
> Created at: 2022-07-09 10:49:15 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179522860  

Can you send me what command did you use to set the user? I have tested with  
  
```  
ACL SETUSER mzimbres on >Jabuticaba  ~* +@all  
```  
The most likely cause of this is that  `AUTH` is failing but the error message is not clear about that. I will try to find a way to improve this.

---

## Comment 4

> Username: Eddie-cz  
> Created at: 2022-07-09 10:57:10 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179523845  

Yes, I did same ACL command. I run exactly your example, except IP address.   
AUTH is not failing, it wouldn't create redis records and return requested data.  
Only thing that is failing is transforming response to set/map type when default user has set password (although i auth using mzimbres as user). That is really strange behaviour, could be some redis bug?

---

## Comment 5

> Username: mzimbres  
> Created at: 2022-07-09 10:57:52 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179523928  

With this simplified version of contaners.cpp  
  
```  
int main()  
{  
   std::map<std::string, int> map  
      {{"key1", 10}, {"key2", 20}, {"key3", 30}};  
  
   request req;  
   req.push("AUTH", "mzimbres", "Jabuttcaba");  
   req.push_range("HSET", "hset-key", map);  
   req.push("HGETALL", "hset-key");  
   req.push("QUIT");  
  
   std::tuple<  
      std::string, // auth  
      std::string, // hgetall  
      std::map<std::string, int>, // exec  
      std::string // quit  
   > resp;  
  
   net::io_context ioc;  
   connection db{ioc};  
   db.async_exec("127.0.0.1", "6379", req, aedis::adapt(resp),  
      [](auto ec, auto) { std::cout << ec.message() << std::endl; });  
   ioc.run();  
  
   print(std::get<2>(resp));  
}  
```  
I get   
  
```  
debian2[0]$ ./containers   
End of file  
key1: 10  
key2: 20  
key3: 30  
```  
when used with the correct password and  
```  
debian2[0]$ ./containers   
Got RESP3 simple-error.  
```  
when I use the wrong password.

---

## Comment 6

> Username: Eddie-cz  
> Created at: 2022-07-09 11:03:44 UTC  
> Updated at: 2022-07-09 11:05:15 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179524682  

I don't get auth failed, as I said. Auth command is still req.push("AUTH", "mzimbres"....  
  
With default user as nopass, I get your output.  
1) "user default on **nopass** ~* &* +@all"  
2) "user mzimbres on #89acbd96ed4b024bc779d8a4e21d1294881f57a62d3b9d699518590a39e0fbd7 ~* resetchannels +@all"  
  
Than I run ACL SETUSER default >somepassword  
  
I rerun your example (with no changes!) and get Expects map type. error.  
I don't know why changing password of user, which is even not authenticated, results in error in resp3 output.

---

## Comment 7

> Username: mzimbres  
> Created at: 2022-07-09 11:08:05 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179525243  

Maybe you are not allowed to execute a command and therefore Redis is returning some error. The simplest way to understand this would be to check what exactly is Redis sending. For example, replace the `std::tuple` from the example with  
  
```  
std::vector<aedis::resp3::node<std::string>> resp;  
```  
and `print(std::get<2>(resp))` with `print(resp)`. If I do that I will get  
```  
debian2[0]$ ./containers   
Got RESP3 simple-error.  
0	simple_error	1	WRONGPASS invalid username-password pair   
```

---

## Comment 8

> Username: Eddie-cz  
> Created at: 2022-07-09 11:17:16 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179526338  

That error is error in your adapters.hpp, ec = error::expects_map_type;, line 215.  
nd.data_type is array, and should be map?  
Replacing tuple with vector gets me  
End of file  
0       simple_string   1       OK 0    number  1       0 0     array   6        1      blob_string     1       key1 1 blob_string      1       10 1    blob_string     1       key2 1  blob_string     1       20 1    blob_string     1      key3 1   blob_string     1       30 0    simple_string   1       OK

---

## Comment 9

> Username: Eddie-cz  
> Created at: 2022-07-09 11:37:26 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179528777  

Resp3 response, when default user has set password:  
"OK\r\n:0\r\n*6\r\n$4\r\nkey1\r\n$2\r\n10\r\n$4\r\nkey2\r\n$2\r\n20\r\n$4\r\nkey3\r\n$2\r\n30\r\n+OK\r\n"   
  
Resp3 response, when default user has NOT SET password (in this case, resp is parsed correctly)  
"OK\r\n:0\r\n%3\r\n$4\r\nkey1\r\n$2\r\n10\r\n$4\r\nkey2\r\n$2\r\n20\r\n$4\r\nkey3\r\n$2\r\n30\r\n+OK\r\n"

---

## Comment 10

> Username: Eddie-cz  
> Created at: 2022-07-09 11:44:25 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179529587  

Looking at resp3 protocol, in first case, there is really array returned, so this is redis bug?

---

## Comment 11

> Username: mzimbres  
> Created at: 2022-07-09 12:05:31 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179532217  

This looks indeed like a Redis bug, the command HSET should only return an array if RESP2 is being used for communication. That could happen for example if the HELLO 3 command is not being sent, which is not the case. It is worth reporting this. I happy Aedis handled this correctly :)

---

## Comment 12

> Username: Eddie-cz  
> Created at: 2022-07-09 13:03:10 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179539951  

Posted issue in Redis, will inform you.

---

## Comment 13

> Username: mzimbres  
> Created at: 2022-07-09 14:27:38 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179553635  

After reading the issue you created in the Redis repo, It looks like it wasn't a good idea to add automatic hello 3. I believe the correct thing is to do then is to let it as a user responsibility. It feels awkward that client and server should agree on the protocol only after AUTH has been sent, but anyway ...

---

## Comment 14

> Username: Eddie-cz  
> Created at: 2022-07-09 14:59:05 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179557950  

Will be solved by https://github.com/mzimbres/aedis/issues/11, sending auth with hello will negotiate required protocol successfully.

---

## Comment 15

> Username: mzimbres  
> Created at: 2022-07-09 20:14:23 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179600353  

I removed sending of HELLO 3 automatically, so now the order of HELLO and AUTH can be inverted, see https://github.com/mzimbres/aedis/blob/6ca0bcc945e306599b9b686971c8c26897e80dda/examples/containers.cpp#L34

---

## Comment 16

> Username: Eddie-cz  
> Created at: 2022-07-09 20:24:57 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179601678  

Commit 6ca0bcc945e306599b9b686971c8c26897e80dda solves this, I don't know what are the plans with high level API, but this could be incorporated into some struct with IP and Port and called automatically with HELLO, which has AUTH params. Hovewer, issue was solved.

---

## Comment 17

> Username: mzimbres  
> Created at: 2022-07-09 20:37:02 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179603188  

I am still thinking about it. I am tending into keeping requests and commands separated from the connection setup. This is going to be important when I add TLS support in the future. BTW, I would like to thank you for helping me improve this. If you send me you real name (either per email or here) I will add it to the Acknowledgement section together with you github profile.

---

## Comment 18

> Username: Eddie-cz  
> Created at: 2022-07-09 20:48:27 UTC  
> Url: https://github.com/boostorg/redis/issues/13#issuecomment-1179604583  

It's Petr Dannhofer, glad to help.
