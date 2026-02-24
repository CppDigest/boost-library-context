# #455 - Help with Query interface design to support format [Closed]

> Username: bruno-viva  
> Created at: 2025-02-25 17:05:50 UTC  
> Updated at: 2025-02-26 04:08:18 UTC  
> Closed at: 2025-02-26 04:08:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/455  

Hi!  
  
I am working with a codebase that defines a query interface like this:  
  
```cpp  
// The query definition.  
class QueryInterface {  
 public:  
  ...  
  
  std::string ConsumeQuery() = 0;  
  virtual QueryInterface& AppendQuery(std::string_view query) = 0;  
  virtual QueryInterface& AppendInt(int64_t number) = 0;  
  virtual QueryInterface& AppendString(std::string string) = 0;  
  virtual QueryInterface& AppendBlob(std::string_view string) = 0;  
};  
```  
  
And you use it like this:  
  
```cpp  
query->AppendQuery("SELECT * FROM table WHERE id = ")  
     .AppendInt(123)  
     .AppendQuery(" AND name = ")  
     .AppendString("John");  
```  
  
The current implementation is using another MySql library. I want to create an implementation using the Boost.MySql version. I can use the format_sql_to utility, and that seems to be working fine.  
  
Templates and virtual functions do not work well together. Can you think of a way at all for me to modify my interface to support doing it the "std::format way"? For instance:  
  
```cpp  
query->AppendQuery("SELECT * FROM table WHERE id = {} AND name = {}")  
     .AppendInt(123)  
     .AppendString("John");  
```  
  
Or even:  
  
```cpp  
query->AppendQuery("SELECT * FROM table WHERE id = {} AND name = {}",  
                   123, "John");  
```  
  
If I understand correctly, I need to be able to pass in a `boost::mysql::constant_string_view` and `std::initializer_list<boost::mysql::format_arg>`, but I am unsure how to do that in my interface without modifying the existing implementation so that it doesn't depend on the Boost.MySql types. Any thoughts or ideas?  
  
Thanks in advance!

---

## Comment 1

> Username: anarthal  
> Created at: 2025-02-25 18:59:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/455#issuecomment-2682995090  

Hi!  
  
The easiest way would be what you propose: `boost::mysql::format_arg` is a type-erased reference to anything that is formattable. Now, this violates the constraint of not having Boost.MySQL types in your public interface.  
  
Your current API has a direct match to `boost::mysql::format_context` (see [this](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__basic_format_context.html) and [this](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__format_context_base.html) reference pages). `format_context` is an accumulator with an `append_raw` function akin to your `AppendQuery`, and an `append_value` akin to all your other methods. (Note that `format_context = basic_format_context<std::string>`). Your implementation becomes:  
  
```cpp  
class QueryInterface  
{  
public:  
    std::string ConsumeQuery() = 0;  
    virtual QueryInterface& AppendQuery(std::string_view query) = 0;  
    virtual QueryInterface& AppendInt(int64_t number) = 0;  
    virtual QueryInterface& AppendString(std::string string) = 0;  
    virtual QueryInterface& AppendBlob(std::string_view string) = 0;  
};  
  
class QueryImpl final : public QueryInterface  
{  
    mysql::format_context ctx_;  
  
public:  
    std::string ConsumeQuery() { return std::move(ctx_).get().value(); }  
    QueryInterface& AppendQuery(std::string_view query) override final  
    {  
        ctx_.append_raw(mysql::runtime(query));  
        return *this;  
    }  
    QueryInterface& AppendInt(std::int64_t number) override final  
    {  
        ctx_.append_value(number);  
        return *this;  
    }  
    QueryInterface& AppendString(std::string string) override final {  
        ctx_.append_value(string);  
        return *this;  
    }  
    QueryInterface& AppendBlob(std::string_view string) override final {  
        // You need to cast this so it gets recognized as a blob  
        boost::span<const unsigned char> blob (reinterpret_cast<const unsigned char*>(string.data(), string.size()));  
        ctx_.append_value(blob);  
        return *this;  
    }  
};  
```  
  
When constructing a `format_context`, you need a `format_options` instance describing connection configuration. The best way to obtain this is usually calling `any_connection::format_opts`. If you don't have a connection, you can use `mysql::format_options{mysql::utf8mb4_charset, true}`, but you need to ensure at handshake time that your connection uses utf8mb4 (it does by default), never set the connection's character set anyhow, and ensure that your server is configured to never use `NO_BACKSLASH_ESCAPES` (I can provide more info on this if you want).  
  
Now this isn't very std::format'ish. I've been trying to write something to use std::variants as format arguments, but it can't be done with today's public API. Incidentally, we have a function in the `detail` namespace that does what you want. I've left you how your code could look like using this function:  
  
```cpp  
// In your header file  
using db_arg = std::variant<std::int64_t, std::string_view, std::span<const unsigned char>>;  
  
class QueryInterface  
{  
public:  
    virtual std::string FormatQuery(std::string_view query, std::initializer_list<db_arg> args) = 0;  
};  
  
// In your .cpp file containing the interface implementation  
mysql::format_arg to_format_arg(db_arg arg)  
{  
    // Convert your variant into a mysql::format_arg  
    return std::visit([](auto a) { return mysql::format_arg("", a); }, arg);  
}  
  
class QueryImpl final : public QueryInterface  
{  
public:  
    std::string FormatQuery(std::string_view query, std::initializer_list<db_arg> args) override final  
    {  
        // Convert your variants into the types required by mysql  
        std::vector<mysql::format_arg> mysql_args;  
        mysql_args.reserve(args.size());  
        for (auto arg : args)  
            mysql_args.push_back(to_format_arg(arg));  
  
        // Unfortunately this can't be written today with the current public API.  
        // I will raise an issue to make vformat_sql_to public.  
        // You can use this for testing purposes  
        mysql::format_context ctx (mysql::format_options{mysql::utf8mb4_charset, true});  
        mysql::detail::vformat_sql_to(  
            ctx,  
            mysql::runtime(query),  
            mysql_args  
        );  
        return std::move(ctx).get().value();  
    }  
};  
```  
  
I will raise an issue to make this `vformat_sql_to` public, since I think it can be useful to other users, too.  
  
Let me know if you have any questions.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: bruno-viva  
> Created at: 2025-02-26 04:08:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/455#issuecomment-2683843882  

Awesome! Thanks so much for your help. I was stuck with the Format part, and that solution seems very convenient! Even if the other API doesn't support format, I am thinking on using std::format with overloads to custom types that use the Append* functions instead for that implementation (the args I can wrap with the visit over the variant).  
  
Will mark this as closed, thanks again for your help @anarthal !
