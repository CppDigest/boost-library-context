# #1083 - How to handle null values? [Closed]

> Username: octopus-prime  
> Created at: 2025-05-11 09:46:27 UTC  
> Updated at: 2025-05-24 11:29:06 UTC  
> Closed at: 2025-05-24 11:29:05 UTC  
> Url: https://github.com/boostorg/json/issues/1083  

Can you provide documentation and working code examples how to deal with null values, please?  
What is the desired stragegy? Working with std::optional?!  
  
So lets try (boost 1.83)  
  
Example:  
  
the jsons to parse: (note: the field 'additionalneeds' is optional / nullable)  
```  
{  
  "firstname": "Mary",  
  "lastname": "Ericsson",  
  "totalprice": 671,  
  "depositpaid": true,  
  "bookingdates": {  
    "checkin": "2015-08-20",  
    "checkout": "2025-04-25"  
  },  
  "additionalneeds": "Breakfast"  
}  
```  
or  
```  
{  
  "firstname": "Susan",  
  "lastname": "Jones",  
  "totalprice": 178,  
  "depositpaid": true,  
  "bookingdates": {  
    "checkin": "2020-08-01",  
    "checkout": "2021-02-18"  
  }  
}  
```  
  
the dto:  
```  
struct Booking {  
  
    struct Bookingdates {  
        std::chrono::year_month_day checkin;  
        std::chrono::year_month_day checkout;  
    };  
  
    static_string<31> firstname;  
    static_string<31> lastname;  
    double totalprice;  
    bool depositpaid;  
    Bookingdates bookingdates;  
    std::optional<std::string> additionalneeds;  
};  
```  
  
trying conversion while reading:  
  
```  
Booking tag_invoke( value_to_tag<Booking>, value const& jv )  
{  
    object const& obj = jv.as_object();  
    return Booking {  
        value_to<std::string_view>( obj.at( "firstname" ) ),  
        value_to<std::string_view>( obj.at( "lastname" ) ),  
        value_to<double>( obj.at( "totalprice" ) ),  
        value_to<bool>( obj.at( "depositpaid" ) ),  
        value_to<Booking::Bookingdates>( obj.at( "bookingdates" ) ),  
        value_to<std::optional<std::string>>( obj.at( "additionalneeds" ) )  
    };  
}  
```  
or  
```  
Booking tag_invoke( value_to_tag<Booking>, value const& jv )  
{  
    object const& obj = jv.as_object();  
    return Booking {  
        value_to<std::string_view>( obj.at( "firstname" ) ),  
        value_to<std::string_view>( obj.at( "lastname" ) ),  
        value_to<double>( obj.at( "totalprice" ) ),  
        value_to<bool>( obj.at( "depositpaid" ) ),  
        value_to<Booking::Bookingdates>( obj.at( "bookingdates" ) ),  
        value_to<std::string>( obj.at( "additionalneeds" ) )  
    };  
}  
```  
  
both variants do complile. but if executed the programm terminates if 'additionalneeds' is null  
  
```  
{"firstname":"Sally","lastname":"Smith","totalprice":583,"depositpaid":false,"bookingdates":{"checkin":"2015-04-28","checkout":"2019-07-18"},"additionalneeds":"Breakfast"}  
->  
Booking = Sally Smith 583 false 2015-04-28 2019-07-18 Breakfast  
```  
  
```  
{"firstname":"Susan","lastname":"Jones","totalprice":178,"depositpaid":true,"bookingdates":{"checkin":"2020-08-01","checkout":"2021-02-18"}}  
->  
Error: out of range [boost.json:17 at /usr/include/boost/json/impl/object.hpp:388:54 in function 'const value &boost::json::object::at(string_view) const &']  
```  
  
So how to deal with nulls?

---

## Comment 1

> Username: octopus-prime  
> Created at: 2025-05-11 10:54:40 UTC  
> Url: https://github.com/boostorg/json/issues/1083#issuecomment-2869686140  

I got the main issue.  
`obj.at( "additionalneeds" )` is throwing exceptions instead of returning a null-value.  
(with a null-value the tag_invoke for optional could produce the empty optional)  
  
So working code becomes unmaintainable, e.g.  
```  
Booking tag_invoke( value_to_tag<Booking>, value const& jv )  
{  
    object const& obj = jv.as_object();  
    std::optional<std::string> additionalneeds = !obj.contains("additionalneeds") ? std::nullopt : std::make_optional<std::string>(value_to<std::string>(obj.at("additionalneeds")));  
    return Booking {  
        value_to<std::string_view>( obj.at( "firstname" ) ),  
        value_to<std::string_view>( obj.at( "lastname" ) ),  
        value_to<double>( obj.at( "totalprice" ) ),  
        value_to<bool>( obj.at( "depositpaid" ) ),  
        value_to<Booking::Bookingdates>( obj.at( "bookingdates" ) ),  
        additionalneeds  
    };  
}  
```  
Because you must write special code now for every nullable field!  
  
btw:  
```  
    @Test  
    void foo() {  
        JsonNode jsonNode = JsonNodeFactory.instance.objectNode(); // no fields  
        JsonNode additionalneeds = jsonNode.get("additionalneeds"); // no exception  
        assertThat(additionalneeds, is(nullValue())); // just null  
    }  
  
    @Test  
    void bar() {  
        Map<String, String> map = Map.of(); // no keys  
        String additionalneeds = map.get("additionalneeds"); // no exception  
        assertThat(additionalneeds, is(nullValue())); // just null  
    }  
  
void qix() {  
    std::map<std::string, Booking> map; // no keys  
    auto found = map.find("foo"); // no exception  
    // found == map.end()  
}  
```  
So throwing exceptions in this use case is at least supprising.

---

## Comment 2

> Username: octopus-prime  
> Created at: 2025-05-11 12:54:08 UTC  
> Url: https://github.com/boostorg/json/issues/1083#issuecomment-2869838647  

Here we go...  
```  
Booking tag_invoke( value_to_tag<Booking>, value const& jv ) {  
    object const& obj = jv.as_object();  
    return Booking {  
        value_to<std::string_view>(find_or_throw(obj, "firstname")),  
        value_to<std::string_view>(find_or_throw(obj, "lastname")),  
        value_to<double>(find_or_throw(obj, "totalprice")),  
        value_to<bool>(find_or_throw(obj, "depositpaid")),  
        value_to<Booking::Bookingdates>(find_or_throw(obj, "bookingdates")),  
        value_to<std::optional<std::string_view>>(find_or_null(obj, "additionalneeds"))  
    };  
}  
```  
  
Easy to change now:  
`value_to<std::string_view>(find_or_throw(obj, "additionalneeds"))`  
vs  
`value_to<std::optional<std::string_view>>(find_or_null(obj, "additionalneeds"))`  
  
With  
```  
const value& find_or_throw(object const& obj, std::string_view key) {  
    return obj.at(key);  
}  
  
const value& find_or_null(object const& obj, std::string_view key) {  
    static const value null_value;  
    const auto found = obj.find(key);  
    return found == obj.end() ? null_value : found->value();  
}  
  
namespace std {  
    template<typename T>  
    optional<T> tag_invoke( value_to_tag<optional<T>>, value const& jv ) {  
        if (jv.is_null()) {  
            return std::nullopt;  
        }  
        return value_to<T>(jv);  
    }  
}  
```

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-05-12 11:18:12 UTC  
> Url: https://github.com/boostorg/json/issues/1083#issuecomment-2872136740  

First of all, I'll tell you the way I recommend to deal with it.  
  
```C++  
BOOST_DESCRIBE_STRUCT(Booking::Bookingdates, (), (checkin, checkout));  
BOOST_DESCRIBE_STRUCT(Booking, (), (firstname, lastname, totalprice, depositpaid, bookingdates, additionalneeds));  
```  
  
E.g. see here: https://godbolt.org/z/YMxGebbEe.  
  
(Note, I used contextual conversion for std::chrono::year_month_day, as it is a cleaner approach than just putting an overload into namespace std::chrono).  
  
Described classes are first mentioned here: https://www.boost.org/doc/libs/release/libs/json/doc/html/json/conversion.html  
Boost.Describe class support is documented here: https://www.boost.org/doc/libs/release/libs/describe/doc/html/describe.html#classes  
  
Now, let's analyse your question.  
  
First of all, the premise is incorrect, you aren't actually asking about `null` values, you are asking about missing values. To my knowledge no language (including e.g. JavaScript) treats missing values and null values as the same thing. Nevertheless, it is very common to treat missing values as equivalent to null values when dealing with data coming from the Internet. Hence, the library-provided conversion function for described classes supports treats missing keys as empty optionals.  
  
But as you yourself have discovered, the library-provided conversion function for optionals cannot even attempt that, because, well, there's no value to use it on.  
  
At one point you suggest that `object::at` returns a null value rather than throw an exception, remarking that this would be the least surprising behaviour. I object to this characterisation. That function is modelled after `std::map::at` and `std::unordered_map::at`, both of which do throw exceptions when the requested key is missing. If we decide to deviate from C++ standard practice (rarely a good idea) and look at other languages, we see that Python's `dict.__getitem__` also throws. JavaScript's object subscript doesn't throw, but it doesn't return `null` either (it returns `undefined`). So, returning `null` from `object::at` for missing keys doesn't actually appear to be popular, which is why it doesn't make sense to deviate from coommon C++ approach here.  
  
`json::object` does provide several member functions to access potentially missing elements without throwing: `find`, `if_contains`, and `try_at`. The first two are pretty much equivalent. You can use them like this:  
  
```c++  
Booking tag_invoke( value_to_tag<Booking>, value const& jv )  
{  
    object const& obj = jv.as_object();  
    auto result = Booking {  
        value_to<std::string_view>( obj.at( "firstname" ) ),  
        value_to<std::string_view>( obj.at( "lastname" ) ),  
        value_to<double>( obj.at( "totalprice" ) ),  
        value_to<bool>( obj.at( "depositpaid" ) ),  
        value_to<Booking::Bookingdates>( obj.at( "bookingdates" ) ),  
        {}  
    };  
    if( auto ptr = obj.if_contains("additionalneeds") )  
        result.additionalneeds = value_to<std::string>(*ptr);  
    return result;  
}  
```  
https://www.boost.org/doc/libs/release/libs/json/doc/html/json/ref/boost__json__object/if_contains.html  
https://www.boost.org/doc/libs/release/libs/json/doc/html/json/ref/boost__json__object/find.html  
  
Now, `try_at` is different in that it returns `boost::system::result`, which is similar to `optional`, but instead of being a value\no value variant it is a value/error variant. It also has an `operator|` that should have worked in your situation: `res | def` returns `*res` if `res.has_value()`, and `def` otherwise. Unforunately it has an overly restrictive type check, so it won't currently (as of Boost 1.88.0) compile in this case. But it can compile with a helper. In https://godbolt.org/z/d196q6z6z I use such a helper to strip reference from `try_at`'s result:  
```c++  
boost::json::value copy_value(boost::json::value const& jv) { return jv; }  
...  
value_to<std::optional<std::string>>( (obj.try_at( "bookingdates" ) & copy_value) | value() ),  
```   
  
Finally, you of course, can use your own helper to return a `null` value for missing keys, as you did in your third post.

---

## Comment 4

> Username: octopus-prime  
> Created at: 2025-05-12 12:17:33 UTC  
> Updated at: 2025-05-12 12:17:52 UTC  
> Url: https://github.com/boostorg/json/issues/1083#issuecomment-2872329483  

Thank you for this answer!  
Pretty nice examples!  
Would love to see something like that in the documentation.

---

## Comment 5

> Username: octopus-prime  
> Created at: 2025-05-24 11:29:05 UTC  
> Url: https://github.com/boostorg/json/issues/1083#issuecomment-2906783274  

just wow! great work :-)
