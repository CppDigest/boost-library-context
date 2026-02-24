# #302 - Make command HELLO optional [Closed]

> Username: AndreyAlifanov  
> Created at: 2025-09-07 05:21:00 UTC  
> Updated at: 2025-09-15 12:17:06 UTC  
> Closed at: 2025-09-15 12:15:12 UTC  
> Url: https://github.com/boostorg/redis/issues/302  

Please, make command HELLO optional.  
  
Some servers implementing RESP3 don't implement command HELLO.  
And it's impossible to use boost.redis with such servers.  
  
But other client libraries and even redis-cli work with these servers just fine.  
  
Examples of such servers: Tarantool DB and Picodata Radix. Both have modules for RESP3.  
  
I think, library should give possibility to use this command, but should not call it itself by default.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-09-07 06:31:49 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3263528851  

AFAIK the main point of HELLO is enforcing the usage of RESP3, vs RESP2, which is the old protocol version used by Redis. At the moment, Boost.Redis only supports RESP3 (Redis v6+). redis-cli supports both protocols.  
  
Do these databases speak RESP2 or RESP3? Is there any place where I can access these databases (preferably as a Docker image) so I can run the library's test suite against them and diagnose problems?

---

## Comment 2

> Username: AndreyAlifanov  
> Created at: 2025-09-07 09:38:17 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3263623363  

As I wrote earlier, these servers speak RESP3, and only RESP3.  
  
It's commercial software, so it's not trivial to get copy for testing.  
You can order a demo for Tarantool DB (https://www.tarantool.io/en/tarantooldb/).  
About Radix I can't say almost nothing - it's quite difficult to get demo-copy.  
  
I did some tests already. When I dropped out hello_op, I succesfully connected to these servers. And commands like "INFO SERVER", "INCR", "SET", "GET", "DEL" and so on were also successful.  
With hello I've got errors at the start and connection cancel.  
  
So I think, it will be useful to have flag in config: to use hello or not for connection.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-09-07 11:14:46 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3263699971  

Great. I've already spoken with @mzimbres and he's open to have this done. I'll implement it when I have the time.

---

## Comment 4

> Username: AndreyAlifanov  
> Created at: 2025-09-07 11:58:33 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3263722677  

Thanks in advance!

---

## Comment 5

> Username: anarthal  
> Created at: 2025-09-07 17:28:15 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3263923097  

Our HELLO also performs authentication (equivalent to the AUTH command) and sets the client name (equivalent to CLIENT SETNAME). @AndreyAlifanov does your system support these commands? Do you have interest in automatic authentication/SETNAME on connection establishment?

---

## Comment 6

> Username: AndreyAlifanov  
> Created at: 2025-09-08 04:42:41 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3264581108  

Yes, we have interest in automatic authentication (for Redis or its clones). But Tarantool/Radix don't support such scenario.  
So we call explicit AUTH command when needed.  
CLIENT SETNAME we don't use at all.

---

## Comment 7

> Username: mzimbres  
> Created at: 2025-09-08 07:41:10 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3265006316  

> So we call explicit AUTH command when needed  
  
For automatic reconnection and failover the connection would have to send AUTH automatically..  
  
At the moment it is also not possible to do manual reconnection properly when AUTH is.needed, because the priority field is not exposed to the user. This field is needed because AUTH has to be sent before any other command that might have been in the queue before thedisconnection.

---

## Comment 8

> Username: AndreyAlifanov  
> Created at: 2025-09-08 07:48:04 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3265027324  

Therefore I ask for **optional** flag, not mandatory.  
It will be my problem if I turn it off.  
  
But maybe it will be better solution to communicate with vendors. To fix their problems with RESP3 implementation.  
Of cause, it's not your question. It's mine.

---

## Comment 9

> Username: anarthal  
> Created at: 2025-09-08 09:35:14 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3265429334  

> Yes, we have interest in automatic authentication (for Redis or its clones). But Tarantool/Radix don't support such scenario.  
So we call explicit AUTH command when needed.  
CLIENT SETNAME we don't use at all.  
  
I'm confused by this. Do you mean that these systems don't support authenticating with HELLO? What I was proposing is having the connection automatically sending an AUTH command (vs a HELLO with an AUTH parameter) on startup when send_hello=false. But I need to know whether these systems support the AUTH command and tge syntax they use.

---

## Comment 10

> Username: AndreyAlifanov  
> Created at: 2025-09-08 10:54:15 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3265740507  

These systems don't support HELLO at all.  
Command AUTH is supported in standard form, as far as I see from redis-cli and documentation.  
  
After sending commands `ACL SETUSER ...` I successfully perform command `AUTH username password`

---

## Comment 11

> Username: anarthal  
> Created at: 2025-09-08 11:04:53 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3265778742  

That's the piece of information I needed, thanks.

---

## Comment 12

> Username: anarthal  
> Created at: 2025-09-10 10:25:00 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3274311093  

@mzimbres I propose the following:  
  
```cpp  
struct config {  
    // ...  
    enum class setup_request_mode {  
        append, // the passed request is appended to the HELLO pipeline generated by the library  
        replace, // the passed request replaces any HELLO pipeline that the library might generate  
    };  
    setup_request_mode setup_mode { setup_request_mode::append };  
    request setup_request;  
};  
```  
  
So the user might:  
* Do nothing, and everything works as it's today.  
* Add some commands to setup_request, and they will be appended to what we send today. This can be used to issue SELECT commands (so we can deprecate `database_index`), subscribe to channels... And it should be fine even with sentinel.  
* Set `setup_mode` to `setup_request_mode::replace`, and the library wouldn't send anything during the connection phase.  
* Set `setup_mode` to `setup_request_mode::replace` and add some commands to `setup_request`. These commands would be sent instead of what's sent today. This allows sending things like AUTH and SELECT in systems that don't support HELLO.  
  
@mzimbres what do you think of this?  
@AndreyAlifanov would this scheme suit you?

---

## Comment 13

> Username: AndreyAlifanov  
> Created at: 2025-09-10 10:30:11 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3274331162  

@anarthal I think, this scheme is good.

---

## Comment 14

> Username: anarthal  
> Created at: 2025-09-15 12:17:06 UTC  
> Url: https://github.com/boostorg/redis/issues/302#issuecomment-3291875158  

@AndreyAlifanov this is now in develop, and will be released in Boost 1.91. To completely disable HELLO (it's in the docs, but just in case):  
  
```cpp  
config cfg;  
cfg.use_setup = true;  
cfg.setup.clear();  
```  
  
You can then populate the request `config::setup` with `AUTH`, `SELECT` or whatever setup commands you like, and they will be automatically executed on connection establishment (or leave it empty and nothing will be executed).
