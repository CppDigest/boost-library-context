# #42 - Documentation unclear on how to test the error-returning functions [Closed]

> Username: ashaduri  
> Created at: 2022-02-15 13:40:49 UTC  
> Updated at: 2022-02-17 23:25:33 UTC  
> Closed at: 2022-02-17 23:25:33 UTC  
> Url: https://github.com/boostorg/leaf/issues/42  

Hi,  
  
I'm trying to test some leaf-enabled code to verify that the error returned by the function is indeed the one that is expected.  
  
I'm using [Catch2](https://github.com/catchorg/Catch2) and I can't really figure out how to test for this. The leaf tutorial does not really go into any detail, while the examples and tests seem to only handle int-based return values, and in a very complicated way.  
  
This is what I'm trying to do:  
```C++  
enum class ParserError {  
	EmptyInput,  
	UnsupportedFormat,  
};  
  
enum class ParserType {  
	Json,  
	Text,  
};  
  
leaf::result<ParserType> detect_input_type(const std::string& input)  
{  
	if (input.empty()) {  
		return leaf::new_error(ParserError::EmptyInput);  
	}  
	// ...  
	return ParserType::Json;  
}  
  
TEST_CASE("FormatDetection", "[parser]")  
{  
	// How to write this?  
	REQUIRE(detect_input_type("").error() == ParserError::EmptyInput);  // compiler error  
}  
```  
  
Thanks!

---

## Comment 1

> Username: zajo  
> Created at: 2022-02-16 06:59:56 UTC  
> Updated at: 2022-02-16 07:01:32 UTC  
> Url: https://github.com/boostorg/leaf/issues/42#issuecomment-1041175763  

Thanks, it is indeed useful to have helper functions for this, I'm working on it but it'll take some time until GHA cycles through and it all gets merged up `develop` and then `master`. In the mean time, use this:  
  
```  
template <class... E, class TryBlock>  
bool try_expect_errors( TryBlock && try_block, E && ... expected )  
{  
    return leaf::try_handle_all(  
        [&]() -> leaf::result<bool>  
        {  
            if( auto r = std::forward<TryBlock>(try_block)() )  
                return false;  
            else  
                return r.error();  
        },  
        [&]( E & e )  
        {  
            return e == std::forward<E>(expected);  
        }...,  
        []  
        {  
            return false;  
        } );  
}  
```  
  
With the above helper function, you can change your code like this:  
  
```  
REQUIRE(try_expect_errors(  
    [] { return detect_input_type(""); },  
    ParserError::EmptyInput) );  
```  
  
The helper function `try_expect_errors` takes a `TryBlock` lambda, and any number of error values, which must have different types (if more than one is provided); it returns `true` iff the passed `try_block` fails AND it produces at least one error object that compares equal to the specified error value of the same type (a single failure may communicate many error objects).  
  
This might look cumbersome, but the reason it has to be done this way is that LEAF is optimized for accessing the `T` in a `result<T>` in case of success, else communicating the failure to the caller without handling it locally, e.g.  
  
```  
leaf::result<foo> parse_foo(const std::string& input)  
{  
    BOOST_LEAF_AUTO(t, detect_input_type(input));  
    switch( t )  
    {  
        case ParserType::Json: return parse_foo_json(input);  
        case ParserType::Text: return parse_foo_text(input);  
        default: return leaf::new_error(ParserError::UnsupportedFormat);  
    }  
}  
```  
  
It this case there is no way to access the error objects, because they are not contained in the `leaf::result` object. They are delivered directly to error handling scopes, which must use `try_handle_some` or `try_handle_all` or `try_catch`.

---

## Comment 2

> Username: ashaduri  
> Created at: 2022-02-16 10:32:00 UTC  
> Url: https://github.com/boostorg/leaf/issues/42#issuecomment-1041341856  

I'll try the code you provided, thank you!

---

## Comment 3

> Username: zajo  
> Created at: 2022-02-16 20:50:50 UTC  
> Url: https://github.com/boostorg/leaf/issues/42#issuecomment-1042298829  

I figured out a better way, I'm writing a similar function which returns `std::variant<T,std::tuple<std::optional<E>...>>`, that is, a function that converts the result into a variant that has a `T` or a tuple of error objects, each of which may be available or not.

---

## Comment 4

> Username: zajo  
> Created at: 2022-02-17 23:25:33 UTC  
> Url: https://github.com/boostorg/leaf/issues/42#issuecomment-1043614234  

See https://boostorg.github.io/leaf/#to_variant. Closing this now, feel free to reopen if there are issues.
