# #194 - compilation error in set_formatter method [Closed]

> Username: BBID  
> Created at: 2022-08-31 12:00:45 UTC  
> Updated at: 2022-09-02 12:54:02 UTC  
> Closed at: 2022-08-31 15:08:31 UTC  
> Url: https://github.com/boostorg/log/issues/194  

I'm trying to set the color using:  
Sink->set_formatter(&coloring_formatter);  
where coloring_formatter hat this signature:  
	void  Logger::coloring_formatter(boost::log::record_view const &rec, boost::log::formatting_ostream &strm)  
which semms to be a correct signature.  
I get this error message when compiling:  
Logger.cpp(56,51): error C2672: 'boost::log::v2_mt_nt6::sinks::basic_formatting_sink_frontend<char>::set_formatter': no matching overloaded function found  
\include\boost-1_78\boost\log\sinks\basic_sink_frontend.hpp(374,10): message : could be 'void boost::log::v2_mt_nt6::sinks::basic_formatting_sink_frontend<char>::set_formatter(const FunT &)'  
  
I cant also build it in linux as well and I dont see if where the problem is. could you please helm me with it?  
Best Reagres,  
Bahman

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-31 15:08:31 UTC  
> Updated at: 2022-08-31 15:13:58 UTC  
> Url: https://github.com/boostorg/log/issues/194#issuecomment-1233065070  

If `Logger::coloring_formatter` is a non-`static` member function of `Logger` then you need to bind a pointer/reference to the `Logger` object on which this function should be called. For example:  
  
```  
class Logger  
{  
public:  
    void coloring_formatter(boost::log::record_view const &rec, boost::log::formatting_ostream &strm);  
  
    void init_logging()  
    {  
        // ...  
        sink->set_formatter(std::bind(&Logger::coloring_formatter, this,  
            std::placeholders::_1, std::placeholders::_2));  
    }  
};  
```  
  
You can use any other method of binding the argument you like, e.g. a C++11 lambda or Boost.Phoenix. The essential part is that the function object signature is `void (boost::log::record_view const &rec, boost::log::formatting_ostream &strm)`.  
  
Alternatively, if you don't need the `Logger` object in `coloring_formatter`, you can make it a `static` member function.  
  
```  
class Logger  
{  
public:  
    static void coloring_formatter(boost::log::record_view const &rec, boost::log::formatting_ostream &strm);  
  
    void init_logging()  
    {  
        // ...  
        sink->set_formatter(&Logger::coloring_formatter);  
    }  
};  
```

---

## Comment 2

> Username: BBID  
> Created at: 2022-09-02 12:54:01 UTC  
> Url: https://github.com/boostorg/log/issues/194#issuecomment-1235471325  

Thank you very much for your help! It worked!
