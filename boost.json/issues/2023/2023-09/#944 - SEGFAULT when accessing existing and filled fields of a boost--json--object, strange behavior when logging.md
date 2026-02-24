# #944 - SEGFAULT when accessing existing and filled fields of a boost::json::object, strange behavior when logging [Closed]

> Username: StormLord07  
> Created at: 2023-09-26 11:10:21 UTC  
> Updated at: 2023-10-06 19:54:01 UTC  
> Closed at: 2023-10-06 19:54:00 UTC  
> Url: https://github.com/boostorg/json/issues/944  

### Version of Boost  
  
Boost 1.83.0  
  
### Steps necessary to reproduce the problem  
  
Ok the  problems seems simple  
  
If you have a function, like this  
  
```  
void callback(boost::json::object &obj) {  
    auto value = boost::json::value_to<std::string>(obj["key"]); // or int64_t doesn't matter  
    #operation with value  
}  
```  
  
it throws an error like  
  
```  
terminate called after throwing an instance of 'boost::detail::with_throw_location<boost::system::system_error>'                                                                                                                               what():  not a number [boost.json:24 at /usr/local/include/boost/json/value.hpp:2374:9 in function 'typename std::enable_if<(std::is_arithmetic<_Tp>::value && (! std::is_same<T, bool>::value)), T>::type boost::json::value::to_number(boost::json::error_code&) const']  
```  
  
or not a string error, which I can't find or reproduce 90% of the time  
  
however if i try to log the obj beforehand  
  
```  
void callback(boost::json::object &obj) {  
    std::string jsonString = boost::json::serialize(obj);  
    BOOST_LOG_TRIVIAL(info) << jsonString;  
    auto value = boost::json::value_to<std::string>(obj["key"]); // or int64_t doesn't matter  
    /*operation with value*/  
}  
```  
  
It doesn't crash at all, I've been running the app with the same data for a day, and it doesn't crash, before it crashed a few minutes in, I can reproduce the error outside my app which I unfortunately can't share. Nothing but added logging was added, When talking with my friend, he had the same error and adding logging also "fixed" the problem. I'm dumb folded, because I don't understand how it happens, nor how to reproduce it with at least some consistency.   
  
P.S. Might help that I call function by a pointer: In different files I have different classes, in `class1` I call function from `class2` that sets it's `call` variable to a pointer of a function and then if conditions are met the function is called like this `call(obj)`, `call` from `class2` in this case points to a `callback` function in `class1`.  
`class2` knows about `class1`, not vice versa.  
  
P.P.S. I know that fields were filled because before the function call I had it logged in another place. and GDB also showed it being filled.  
  
### All relevant compiler information  
  
make, g++ 14, cmake 3.26.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-09-26 11:24:36 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1735341303  

I need more info to reproduce the problem. So far looks like in one case you have the key in the object, and in another one you don't. But as I understand, this isn't the case.

---

## Comment 2

> Username: StormLord07  
> Created at: 2023-09-26 13:30:40 UTC  
> Updated at: 2023-09-26 14:04:05 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1735549501  

telegrambot.h  
 ```  
 void name(boost::json::object &obj) {  
    auto id_str = boost::json::value_to<std::string>(obj["id"]);  
    auto api_key = boost::json::value_to<std::string>(obj["api_key"]);  
    auto amount = boost::json::value_to<std::string>(obj["amount"]);  
  
    // Construct the output string  
    auto out = "ID: " + id_str +  
      "\namount: " + amount;  
    if (obj.contains("stop")) {  
        out += "\nLimit reached";  
    }  
  
    // Send the message  
    try {  
        bot->getApi().sendMessage(userID_API_key.at(api_key), out);  
    }  
    catch (std::exception &e) {  
        BOOST_LOG_TRIVIAL(info) << "error in fun name(boost::json::object &obj): " << e.what();  
    }  
}  
```  
  
handler.cpp  
  
```  
void Bot::setCallbackFunction(CallbackFunction function_ptr) {  
    this->call = function_ptr;  
}  
  
void Bot::telegram_callback() {  
    if (!this->callbackTransactions.empty()) {  
        for (auto transaction : callbackTransactions) {  
                transaction["api_key"] = api_key + tr_id;  
                if (this->limit.load() < (int64_t)minBuyoutValue)  
                {  
                        transaction["stop"] = true;  
                }  
                call(transaction);  
  
        }  
        ThreadSafe::vector<json::object> empty;  
        this->callbackTransactions.swap(empty);  
    }  
}  
```  
  
This part of code is separated from the main loop and is executed every minute at the start. Transaction always have all fields that are not "stop", And this doesn't work it fails on `id_str` (or whatever is accesed first)  
however adding  
  
```  
    std::string jsonString = boost::json::serialize(obj);  
    BOOST_LOG_TRIVIAL(info) << jsonString;  
```  
at the start of the `name` function makes everything work fine and without any errors  
  
It was tested on almost the same data (API response), it can't have failed anywhere before this because if it was such, the fail would have happened much earlier in a program lifecycle

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-09-27 08:33:20 UTC  
> Updated at: 2023-09-27 08:52:59 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1736948532  

This still isn't enough information to understand why you're having this problem. I need something I can try locally. Without that I can only speculate.  
  
So, here's the speculation. You use `ThreadSafe::vector`, which implies using threads. If a key is missing before logging and is present after the logging, then I suspect another thread adds that key, while logging is performed. This can happen if you're not synchronising accesses to your data properly.  
  
Can you rewrite your example to not use threads to see if that is indeed the case?

---

## Comment 4

> Username: StormLord07  
> Created at: 2023-09-27 08:39:39 UTC  
> Updated at: 2023-09-27 11:32:45 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1736958264  

I'll try to reproduce this in other app when I'll have free time, Maybe adding value in threads are the case, but it's still weird, because the above code executed only after threads have exited.  
  
P.S. Why serializing "fixes" issue if the problem is multithreading the problem is in a called function, that has a copy of the value at the moment of calling.

---

## Comment 5

> Username: StormLord07  
> Created at: 2023-09-27 12:08:49 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1737266127  

I have tried to reproduce that in both multithreaded environment and in the one thread, just a very simplified version of the functions.   
  
```  
#include <iostream>  
#include <thread>  
#include <chrono>  
#include <atomic>  
#include <boost/json.hpp>  
#include <boost/log/trivial.hpp>  
#include "threadSafeVector.h"  
  
class Bot {  
public:  
    using CallbackFunction = std::function<void(boost::json::object &)>;  
  
    Bot(const std::string &api, int64_t id)  
        : api_key(api), tr_id(id) {  
    }  
  
    void setCallbackFunction(CallbackFunction function_ptr) {  
        this->call = function_ptr;  
    }  
  
    void processTransactions(boost::json::object transactions) {  
        auto now = std::chrono::system_clock::now();  
        std::time_t time_now = std::chrono::system_clock::to_time_t(now);  
        std::tm *now_tm = std::localtime(&time_now);  
  
        if (now_tm->tm_sec > 2) {  
            call(transactions);  
            // For demonstration, log transaction instead of sending to Telegram  
            BOOST_LOG_TRIVIAL(info) << "Processed transaction with ID: " << boost::json::value_to<std::string>(transactions["amount"]);  
        }  
    }  
  
private:  
    std::string api_key;  
    int64_t tr_id;  
    CallbackFunction call;  
};  
  
ThreadSafe::vector<boost::json::object> callbackTransactions;  
  
void addTransactions(const std::string &jsonData) {  
    boost::json::value val = boost::json::parse(jsonData);  
    boost::json::object obj = val.as_object();  
  
    if (obj["result"].as_int64() == 1) {  
        for (auto &payout : obj["payouts"].as_array()) {  
            callbackTransactions.push_back(payout.as_object());  
        }  
    }  
}  
  
int main() {  
    Bot bot("api_key_here", 1234);  
  
    bot.setCallbackFunction([](boost::json::object &obj) {  
        // ... Do something with transaction (this is where you'd normally send to Telegram)  
        });  
  
    std::string jsonData = R"({  
        "result": 1,  
        "payouts": [  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"},  
            {"id": 101282150, "amount": "3000.00"}  
        ]  
    })";  
  
    while (true) {  
        auto now = std::chrono::system_clock::now();  
        std::time_t time_now = std::chrono::system_clock::to_time_t(now);  
        std::tm *now_tm = std::localtime(&time_now);  
  
        if (now_tm->tm_sec <= 2) {  
            // Add transactions in the first two seconds  
            addTransactions(jsonData);  
            std::this_thread::sleep_for(std::chrono::milliseconds(50));  
        }  
        else {  
            for (auto transaction : callbackTransactions) {  
                bot.processTransactions(transaction);  
  
            }  
            callbackTransactions.clear();  
        }  
    }  
  
    return 0;  
}  
```  
  
Still couldn't reproduce it, this had the error after running for an hour, and then nothing, the error is more frequent in a real system, but unfortunately hardly reproducible in a test environment.  
  
I'll show this thread to my friend who had the same issue, maybe he'll help a little more

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-09-28 09:16:44 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1738777061  

> P.S. Why serializing "fixes" issue if the problem is multithreading the problem is in a called function, that has a copy of the value at the moment of calling.  
  
```C++  
void name(boost::json::object &obj) {  
```  
Where's the copy?

---

## Comment 7

> Username: StormLord07  
> Created at: 2023-09-28 09:59:42 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1738846454  

Ok, my mistake, I overlooked that, in other commit i was creating a copy of transaction, that i was passing into call nothing was changed, moreover it's still strange, I use "custom" thread safe vector that mutex locks container untill finished [this](https://github.com/Unit-chain/Unit/blob/7278934590505ae3a468081a3c998132ef9c68d4/unit/libdevcore/datastructures/containers/vector.h#L13)

---

## Comment 8

> Username: grisumbras  
> Created at: 2023-09-28 10:21:16 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1738876204  

Again, it's hard to investigate without seeing the actual code that misbehaves. I can speculate, that since your vector synchronizes on begin and end, but does not synchronize on iterator increments and iterator dereference, the objects accessed can be left in unmodified state depending on chance. It's better to have a sort of queue API with enqueue/dequeue. And after dequeueing you don't access the shared data between threads.

---

## Comment 9

> Username: StormLord07  
> Created at: 2023-10-06 19:54:01 UTC  
> Url: https://github.com/boostorg/json/issues/944#issuecomment-1751334172  

Ok, after a lot of testing. The problem was indeed in thread syncronisation, somehow, sometimes, telegram interface decided to work superfast and make everything in milliseconds insted of a second which my system was mostly designed for, and a header for a thread safe vector isn't that thread safe as wanted.  
  
Conclusion: always check what code you use.
