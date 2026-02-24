# #434 - Jupyter Kernel Notebook Dies After Starting Threaded Class [Closed]

> Username: MasterBe  
> Created at: 2024-04-28 00:47:50 UTC  
> Updated at: 2024-05-26 15:33:30 UTC  
> Closed at: 2024-05-26 15:33:30 UTC  
> Url: https://github.com/boostorg/python/issues/434  

I am using Boost Python to expose a function that holds an internal thread, the class has ```start``` and ```stop``` functions that only control an internal loop. The code for ```MyClass``` is:  
  
```  
#include <iostream>  
#include <atomic>  
#include <thread>  
#include <boost/make_shared.hpp>  
#include <boost/python.hpp>  
#include <boost/python/module.hpp>  
#include <boost/python/make_constructor.hpp>  
  
class MyClass  
{  
private:  
    std::atomic<bool> running_;  
    std::thread worker_;  
    std::function<std::string(std::string)> m_op;  
  
  
    void printMessage()  
    {  
        std::string s = "hello";  
        while (running_)  
        {  
            s = m_op(s);  
            std::this_thread::sleep_for(std::chrono::seconds(1));  
        }  
    }  
  
public:  
    MyClass() : running_(false) {}  
    MyClass(const std::function<std::string(std::string)>& op) : running_(false), m_op(op) {}  
  
    void start()  
    {  
        if (!running_)  
        {  
            running_ = true;  
            worker_ = std::thread(&MyClass::printMessage, this);  
        }  
    }  
  
    void stop()  
    {  
        if (running_)  
        {  
            running_ = false;  
            worker_.join();  
        }  
    }  
};  
```  
  
I then create a helper function to pass a callback as:  
  
```  
template<class output_tp, class... input_tps>  
struct function_helper  
{  
    function_helper(const boost::python::object& op) : m_op(op) {}  
  
    output_tp operator()(input_tps&&... args)  
    {  
        return boost::python::call<output_tp>(m_op.ptr(), std::forward<input_tps>(args)...);  
    }  
  
private:  
    boost::python::object m_op;  
};  
  
boost::shared_ptr<MyClass> makeMakeClass(const boost::python::object& obj)  
{  
    return boost::make_shared<MyClass>(function_helper<std::string, std::string>(obj));  
}  
```  
  
I then create my module:  
  
```  
BOOST_PYTHON_MODULE(draft)  
{  
    using namespace boost::python;  
  
    class_<MyClass, bases<>, boost::shared_ptr<MyClass>, boost::noncopyable>("MyClass")  
        .def("__init__", make_constructor(makeMakeClass))  
        .def("start", &MyClass::start)  
        .def("stop", &MyClass::stop);  
}  
```  
  
I use the ```draft``` module in jupyter as follows attached   
  
<img width="350" alt="jupyter" src="https://github.com/boostorg/python/assets/28602385/05ac8a18-16ac-4fd0-bca1-34dfcf8b2648">  
  
  
The first run causes a crash as in  
  
![kernel](https://github.com/boostorg/python/assets/28602385/ca283faf-43dc-4d71-8d7b-2c445be807a9)  
  
  
The second run is not throwing but produces nothing. Could you please help me make this work.

---

## Comment 1

> Username: MasterBe  
> Created at: 2024-04-28 12:21:24 UTC  
> Url: https://github.com/boostorg/python/issues/434#issuecomment-2081467320  

**UPDATE**  
I figured a partial source of the problem, this line ```boost::python::call``` is what is causing issues, wondering if it's a memory thing when copying ```const boost::python::object& op```. Still stuck on how to make to the above work though.

---

## Comment 2

> Username: MasterBe  
> Created at: 2024-05-05 11:50:40 UTC  
> Url: https://github.com/boostorg/python/issues/434#issuecomment-2094775183  

@stefanseefeld could you please take a look at the above. Still blocked on this for days :)

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2024-05-06 07:29:46 UTC  
> Url: https://github.com/boostorg/python/issues/434#issuecomment-2095355782  

I'm travelling till the end of the month and thus unable to look into this  
in depth. I strongly recommend breaking the problem up to get to the bottom  
of the issue:  
  
* you probably don't jupyter to see the issue, i.e. try to make this a  
standard module or even executable that you then can more easily debug  
* what role does the threading play ? perhaps you need to acquire/ release  
the GIL not to corrupt the python runtime from your worker thread ?  
  
On Sun, May 5, 2024, 13:51 MasterBe ***@***.***> wrote:  
  
> @stefanseefeld <https://github.com/stefanseefeld> could you please take a  
> look at the above. Still blocked on this for days :)  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/434#issuecomment-2094775183>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAJPRV52QZYOJBO7TFQ7RITZAYMKLAVCNFSM6AAAAABG4QJRPWVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDAOJUG43TKMJYGM>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 4

> Username: MasterBe  
> Created at: 2024-05-26 15:31:34 UTC  
> Url: https://github.com/boostorg/python/issues/434#issuecomment-2132260368  

@stefanseefeld Thanks very much for the hint. Problem solved it was indeed acquiring/ releasing the GIL
