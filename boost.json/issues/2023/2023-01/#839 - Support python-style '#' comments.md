# #839 - Support python-style '#' comments [Open]

> Username: rh-m2  
> Created at: 2023-01-13 15:31:18 UTC  
> Updated at: 2023-01-16 12:33:22 UTC  
> Url: https://github.com/boostorg/json/issues/839  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
1.81  
  
### Steps necessary to reproduce the problem  
  
```  
auto parse = [](json::string_view s)  
{  
    return json::parse(  
        s,  
        json::storage_ptr(),  
        json::parse_options { .allow_comments        = true,  
                              .allow_trailing_commas = true });  
};  
  
parse(R"_json_(  
{  
    "exchange": "bitmex",  
    "from": "2019-10-01",  
    "to": "2019-10-02",  
    "symbols": ["XBTUSD", "ETHUSD"],  
    "withDisconnectMessages": true,  
    "dataTypes": ["trade", "book_change", "book_snapshot_10_100ms"],  
    # other available data types examples:  
    # 'book_snapshot_10_100ms', 'derivative_ticker', 'quote',  
    # 'trade_bar_10ms', 'trade_bar_10s'  
}  
)_json_")  
```  
  
This JSON will be parsed by python's parser, but not by boost.json  
  
### All relevant compiler information  
  
Any  
  
### Arguments for supporting this  
  
I am writing a back testing and systematic trading engine. The engine is configured with JSON, picked up either off a config file or via a data bus such as RabbitMQ.  
  
Some components of the system are written in C++ and some in Python. It would be useful to be able to add comments to config files that were parsed by both kinds of components.  
  
### Workaround:  
  
```  
parse(R"_json_(  
{  
    "exchange": "bitmex",  
    "from": "2019-10-01",  
    "to": "2019-10-02",  
    "symbols": ["XBTUSD", "ETHUSD"],  
    "withDisconnectMessages": true,  
    "dataTypes": ["trade", "book_change", "book_snapshot_10_100ms"],  
    "_1": "other available data types examples:",  
    "_2": 'book_snapshot_10_100ms', 'derivative_ticker', 'quote','trade_bar_10ms', 'trade_bar_10s'"  
}  
)_json_")  
```  
  
But you know, yuk!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-01-13 15:47:14 UTC  
> Url: https://github.com/boostorg/json/issues/839#issuecomment-1382039711  

In theory this can be done without harming performance when allow_comments=false. Or at least it will be no worse than how we currently support the C and C++ style comments.

---

## Comment 2

> Username: rh-m2  
> Created at: 2023-01-16 12:33:22 UTC  
> Url: https://github.com/boostorg/json/issues/839#issuecomment-1383988897  

Another workaround:  
  
```  
std::string  
depython(std::string const &in)  
{  
    static const auto reg = std::regex("^[ \t]*#.+(#\n|$)");  
    return std::regex_replace(in, reg, "");  
}  
  
auto val = json::parse(depython(json_text));  
```
