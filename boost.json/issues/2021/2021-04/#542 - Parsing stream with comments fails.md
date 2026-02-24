# #542 - Parsing stream with comments fails [Closed]

> Username: zandreika  
> Created at: 2021-04-15 14:41:33 UTC  
> Updated at: 2021-04-17 05:13:01 UTC  
> Closed at: 2021-04-17 05:13:01 UTC  
> Url: https://github.com/boostorg/json/issues/542  

if get example from [here](https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/input_output.html#json.input_output.parsing.streaming_parser) and modify it to allow parsing comments, `finish()` returns `incomplete JSON` error.  
  
```  
boost::json::value read_json( std::istream& is, std::error_code& ec )  
{  
    boost::json::parse_options options;  
    options.allow_comments = true;  
    boost::json::stream_parser p({}, options);  
    std::string line;  
    while( std::getline( is, line ) )  
    {  
        p.write( line, ec );  
        if( ec )  
            return nullptr;  
    }  
    p.finish( ec );  
    if( ec )  
        return nullptr;  
    return p.release();  
}  
  
void test()  
{  
    std::stringstream s;  
    s << "{\n"  
         "\"key\": \"value\",\n"  
         "\"key2\":\"value\" //comment \n"  
         "}";  
    std::error_code ec;  
    auto res = read_json(s, ec);  
    // res == null  
    // ec == boost::json::error::incomplete  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-04-15 14:54:56 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820494729  

This test passes:  
```  
    // https://github.com/boostorg/json/issues/542  
    void  
    testIssue542()  
    {  
        parse_options po;  
        po.allow_comments = true;  
        stream_parser p({}, po);  
        p.write("{\n");  
        p.write("\"k1\": \"v1\",\n");  
        p.write("\"k2\": \"v2\" //comment\n");  
        p.write("}");  
        error_code ec;  
        p.finish(ec);  
        BOOST_TEST(! ec);  
    }  
```  
  
I notice that the last line of your input string does not have a newline. What happens when you use this code instead:  
  
```  
    s << "{\n"  
         "\"key\": \"value\",\n"  
         "\"key2\":\"value\" //comment \n"  
         "}\n";  
```

---

## Comment 2

> Username: zandreika  
> Created at: 2021-04-15 17:44:51 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820614576  

> What happens when you use this code instead.   
  
`incomplete JSON` again

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-04-15 18:17:57 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820635005  

What likely happens is that getline eats newline characters.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-04-15 18:24:15 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820638941  

oh snap... :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-04-15 18:27:38 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820641128  

The second part of this test fails:  
```  
    // https://github.com/boostorg/json/issues/542  
    void  
    testIssue542()  
    {  
        {  
            parse_options po;  
            po.allow_comments = true;  
            stream_parser p({}, po);  
            p.write("{\n");  
            p.write("\"k1\": \"v1\",\n");  
            p.write("\"k2\": \"v2\" //comment\n");  
            p.write("}");  
            error_code ec;  
            p.finish(ec);  
            BOOST_TEST(! ec);  
        }  
        {  
            auto const read_json = [](  
                std::istream& is,  
                error_code& ec ) ->  
                    json::value  
            {  
                parse_options options;  
                options.allow_comments = true;  
                stream_parser p({}, options);  
                std::string line;  
                while( std::getline( is, line ) )  
                {  
                    p.write( line, ec );  
                    if( ec )  
                        return nullptr;  
                }  
                p.finish( ec );  
                if( ec )  
                    return value{};  
                return nullptr;  
            };  
  
            std::stringstream s;  
            s << "{\n"  
                 "\"key\": \"value\",\n"  
                 "\"key2\":\"value\" //comment \n"  
                 "}";  
            error_code ec;  
            auto res = read_json(s, ec);  
            BOOST_TEST(! ec.failed());  
        }  
    }  
```  
  
The debugger shows that `std::getline` is consuming the newline. Changing to a C-style comment causes the test to pass:  
  
```  
s << "{\n"  
        "\"key\": \"value\",\n"  
        "\"key2\":\"value\" /*comment*/ \n"  
        "}";  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-04-15 18:27:45 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820641203  

Working as designed

---

## Comment 7

> Username: zandreika  
> Created at: 2021-04-15 18:29:24 UTC  
> Url: https://github.com/boostorg/json/issues/542#issuecomment-820642215  

```  
    parse_options po;  
    po.allow_comments = true;  
    stream_parser p({}, po);  
    std::stringstream s;  
    s << "{\n"  
         "\"k1\": \"v1\",\n"  
         "\"k2\": \"v2\" //comment\n"  
         "}";  
    std::string line(std::istreambuf_iterator<char>(s), {});  
    p.write(line);  
    std::error_code ec;  
    p.finish(ec);  
  ```  
  also works :)
