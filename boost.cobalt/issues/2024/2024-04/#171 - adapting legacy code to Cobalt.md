# #171 - adapting legacy code to Cobalt [Closed]

> Username: notzain  
> Created at: 2024-04-15 17:46:23 UTC  
> Updated at: 2024-11-13 03:59:57 UTC  
> Closed at: 2024-11-13 03:59:57 UTC  
> Url: https://github.com/boostorg/cobalt/issues/171  

Looking into integrating Cobalt into our application; the integration with asio+coroutines so far has been great. Hit a roadblock when we had to integrate into 3rd party libraries and legacy code.   
  
For example, imagine  
```cpp  
// handled on a different thread  
void do_request(int type, void(*)(int result, void* userdata), void* userdata);  
  
// Is this possible? Or anything else?  
cobalt::promise<int> coro_as_future(int type) {  
  std::promise<int> p;  
  auto f = p.get_future();  
  do_request(type, [] (int result, void* userdata) {  
     auto* p = static_cast<std::promise<int>*>(userdata);  
     p->set_value(result);  
  }, &p);   
 co_return co_await f;  
}  
  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-04-15 22:27:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/171#issuecomment-2057915238  

You'd need to write your own [`awaitable`](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#awaitable) here. For the `do_request` we'd need to assume that the function passed is guaranteed to get invoked   
 - exactly once  
 -  not recursively  
 -  on the same thread  
  
Changing either of those will complicate the example.  
  
You can write it like this:  
  
```cpp  
struct request_op : cobalt::op<int>  
{  
   int type;  
   cobalt::completion_handler<int> complete; // this will also hold the coroutine lifetime  
   request_op(int type) : type(type) {}  
     
   void initiate(cobalt::completion_handler<int> complete) override   
   {  
    this->complete = std::move(complete);  
    do_request(type,  
                       +[](int result, void * data)  
                       {  
                         auto & ch = *static_cast<cobalt::completion_handler<int>*>(data);  
                        ch(result);  
                       }, &this->complete);  
   }  
};  
```  
  
This creates an awaitable in a more convenient way, but you can also just hand write your own. If your API is more complicated, please follow up with more questions.

---

## Comment 2

> Username: notzain  
> Created at: 2024-04-16 17:42:15 UTC  
> Updated at: 2024-04-16 17:46:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/171#issuecomment-2059627180  

Hi, thank you for your reply. Just for extra context, I'm trying to coro-ify a snmp library.  
  
I've tried your example code, and the only change I had to make was changing `cobalt::completion_handler<int>` to `std::optional< cobalt::completion_handler<int> >` as the completion_handler could not be default constructed (unless I missed something).  
  
The process was surprisingly painless with the given example! I had expected a lot more grief and hair pulling.  
  
Some questions:  
  
1.  
> assume that the function passed is guaranteed to get invoked on the same thread.   
  
What exactly do you mean here? The `do_request()` will be called on `this_coro::executor`, but it by itself has its own worker thread that does processing and will call the callback function provided. This would be fine, and it would be a problem if the do_request was invoked from inside `initiate` on another thread?  
  
2. In case of the completion_handler, how would you propagate errors or exceptions? Or would you templatize on `variant<T, std::exception_ptr>`  
  
3. Prior to your example I hacked around, and I got something working but I'm sure I did something horrible. Sharing it for fun, would like to know why it could be a bad solution, besides the overhead of calling `run`  
```cpp  
cobalt::detached send_result(cobalt::channel<int>& ch, int result) {  
  co_await ch.write(result);  
}  
  
cobalt::promise<int> coro_evil(int type) {  
  cobalt::channel<int> channel;  
  do_request(type, [] (int result, void* userdata) {  
     auto* ch= static_cast<cobalt::channel<int>*>(userdata);  
     cobalt::run(send_message(*ch, result));  
  }, &channel);   
 co_return co_await channel.read();  
}  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-04-16 22:20:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/171#issuecomment-2060008513  

1. If you resume the completion handler on a different thread your coroutine will switch threads, which can cause major issues.  
  
```cpp  
// on thread 1  
co_await request_op(42);  
// on thread 2  
```  
  
In that case you'll need to use `asio::dispatch(asio::append(std::move(ch), result))` instead of `ch(result)`. Then the resumption will be on the executor (i.e. usually the same thread) as you initiated from.  
  
2. That should follow asio conventions, i.e. put the error first in a tuple. That is `system::error_code` or `std::exception_ptr`.  
  
```cpp  
struct op_with_error : cobalt::op<system::error_code, int> {...};  
  
int i = co_await op_with_error(); // will turn an error_code into a thrown system::system_error  
  
system::result<int> r = co_await cobalt::as_result(op_with_error()); // akin to your variant  
std::tuple<system::error_code, int> t = co_await cobalt::as_tuple(op_with_error());   
```  
  
4. Looks like a nail :).

---

## Comment 4

> Username: notzain  
> Created at: 2024-04-23 00:17:13 UTC  
> Updated at: 2024-04-23 00:20:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/171#issuecomment-2071160701  

Hi, I was able to somewhat get something working with your suggestion. I've stumbled on a strange issue, I cannot `await` the call multiple times in a row and the call seems to block the executor context.  
  
```cpp  
boost::cobalt::main co_main(int argc, char* argv[])  
{  
    auto snmp = std::make_shared<SnmpClient>();  
  
   // first iteration works, second does not  
    for (int i = 0; i < 3; ++i) {  
        try {  
            auto result = co_await snmp->get( fmt::format("192.168.1.{}", i), SYSTEM_UPTIME);  
            if (auto* oid = result.try_get<Oid>()) {  
                infoln("Received SNMP = reply from {}: {}", ip, oid->value);  
            } else if (auto* status = result.try_get<SnmpStatus>()) {  
                errorln("SNMP request to {} failed: {}", ip, (int)*status);  
            }  
        } catch (const SnmpException& e) {  
            errorln("SNMP probe request to {} failed: {}", ip, e.what());  
        }  
    }  
  
    // SnmpClient destructor has been already called before it reaches this line!  
    infoln("All requests completed");  
  
   // Application does not exit as well, CTRL+C does not respond either  
  
    co_return 0;  
}  
```  
  
```cpp  
using CompletionHandler = boost::cobalt::completion_handler<std::exception_ptr, std::vector<Oid>>;  
  
struct SnmpOp : boost::cobalt::op<std::exception_ptr, std::vector<Oid>> // enable_op is to use ADL  
{  
    std::shared_ptr<SnmpClient> client; // Wrapper class that inherits from shared_from_this to extend lifetime  
    std::unique_ptr<Snmp_pp::SnmpTarget> target;  
    std::vector<std::string> oid_def;  
  
    Optional<CompletionHandler> complete;  
  
    SnmpOp(std::shared_ptr<SnmpClient> cli, std::unique_ptr<Snmp_pp::SnmpTarget> tar, std::string oid)  
        : client(std::move(cli))  
        , target(std::move(tar))  
        , oid_def({ std::move(oid) })  
    {  
    }  
  
    ~SnmpOp() override  
    {  
        infoln("Destroying SnmpOp");  
    }  
  
    void initiate(CompletionHandler ch) override  
    {  
        infoln("Initiating SnmpOp {}", target->get_address().get_printable());  
        this->complete.emplace(std::move(ch));  
  
        Snmp_pp::Pdu pdu;  
        Snmp_pp::Vb var_bind;  
  
        for (const auto& def : oid_def) {  
            Snmp_pp::Oid oid(def.data());  
            var_bind.set_oid(oid);  
            pdu += var_bind;  
        }  
  
        client->m_client->get(pdu, *target, &co_callback, complete.get_ptr());  
    }  
  
    static void co_callback(int reason, Snmp_pp::Snmp* snmp, Snmp_pp::Pdu& pdu, Snmp_pp::SnmpTarget& target, void* callback_ptr)  
    {  
        auto& completion_handler(*static_cast<CompletionHandler*>(callback_ptr));  
  
        infoln("Callback SnmpOp {}, {}", target.get_address().get_printable(), reason);  
        if (reason != SNMP_CLASS_ASYNC_RESPONSE) {  
            infoln("Timeout on {}", target.get_address().get_printable());  
            completion_handler(std::make_exception_ptr(SnmpException(SnmpStatus::Timeout)), {});  
            return;  
        }  
  
        if (const auto pdu_status = pdu.get_error_status()) {  
            std::string oids;  
            for (int i = 0; i < pdu.get_vb_count(); ++i) {  
                oids += pdu[i].get_printable_oid();  
                oids += ", ";  
            }  
            errorln("{} ({}), {}, {}",  
                snmp->error_msg(pdu_status), pdu_status,  
                target.get_address().get_printable(),  
                oids);  
  
            infoln("PDU error on {}", target.get_address().get_printable());  
            completion_handler(std::make_exception_ptr(SnmpException(static_cast<SnmpStatus>(pdu.get_error_status()))), {});  
            return;  
        }  
  
        std::vector<Oid> oids;  
        Snmp_pp::Vb next_var_bind;  
        for (int i = 0; i < pdu.get_vb_count(); ++i) {  
            pdu.get_vb(next_var_bind, i);  
            oids.push_back(oid_from_vb(next_var_bind));  
        }  
  
        infoln("Success on {}", target.get_address().get_printable());  
        completion_handler(nullptr, oids);  
    }  
};  
  
```  
  
```log  
[2024-04-23 02:16:49.765] [info] [SnmpClient.cpp:870] Starting get 192.168.1.1  
[2024-04-23 02:16:49.765] [info] [SnmpClient.cpp:813] Initiating SnmpOp 192.168.1.1/161  
[2024-04-23 02:16:49.965] [info] [SnmpClient.cpp:833] Callback SnmpOp 192.168.1.1/161, -5  
[2024-04-23 02:16:49.965] [info] [SnmpClient.cpp:835] Timeout on 192.168.1.1/161  
[2024-04-23 02:16:49.966] [info] [SnmpClient.cpp:808] Destroying SnmpOp  
[2024-04-23 02:16:49.966] [error] [main.cpp:125] SNMP probe request to 192.168.1.1 failed: Timeout  
[2024-04-23 02:16:49.967] [info] [SnmpClient.cpp:553] Destroying SnmpClient  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-04-26 16:45:06 UTC  
> Url: https://github.com/boostorg/cobalt/issues/171#issuecomment-2079737549  

It might be because you move the client into the operation here, but I don't the code of `get`.  
  
```cpp  
        : client(std::move(cli))  
```  
  
If you need some help with a non public code-base, send me an e-mail.
