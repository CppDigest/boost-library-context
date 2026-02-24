# #40 Hierarchical error handling [Closed]

> Username: vector-of-bool  
> Created at: 2022-01-08 07:39:34 UTC  
> Updated at: 2024-02-07 07:59:04 UTC  
> Closed at: 2024-02-07 07:59:03 UTC  
> Url: https://github.com/boostorg/leaf/pull/40  

This PR is part suggestion, part implementation. If you can think of an easier to do what I'm trying to do, I'm all ears.  
  
I have an application that has a lot of duplication in terms of issuing diagnostics for error handling, and I want to keep the error handling localized as much as possible. It has an intrinsic hierarchy that mirrors the error-generating code, but I can't represent that hierarchy well with just a flat sequence of handlers. Shortened example:  
  
```c++  
leaf::try_catch(  
    [] { return open_project (); },  
    [](e_project_path path, e_missing_file missing) {  
        log("Error while opening project in {}", path.value);  
        log("Missing file: {}", missing.value);  
    },  
    [](e_project_path path, e_json_string json_str, e_json_parse_error json_err) {  
        log("Error while opening project in {}", path.value);  
        log("Error in JSON '{}'", json_str.value);  
        log("Invalid JSON: {}", json_err.value);  
    },  
    [](e_project_path path, e_json_string json_str, e_json_schema_error err) {  
        log("Error while opening project in {}", path.value);  
        log("Error in JSON '{}'", json_str.value);  
        log("JSON data is invalid: {}", err.value);  
    });  
```  
  
In reality there can be dozens of handlers that have some amount of duplication between them, and it can become difficult to browse and update as error conditions are added.  
  
This PR adds `leaf::handle_more` that can be used to do nested error handling. It looks like this:  
  
```c++  
leaf::try_catch(  
  [] { return open_project (); },  
  [](e_project_path path) {  
    log("Error while opening project in {}", path.value);  
    return leaf::handle_more(  
      [](e_missing_file missing) {  
        log("Missing file: {}", missing.value);  
      },  
      [](e_json_string json_str) {  
        log("Error in JSON '{}'", json_str.value);  
        return leaf::handle_more(  
          [](e_json_parse_error err)  
            { log("Invalid JSON: {}", err.value); },  
          [](e_json_schema_error err)   
            { log("JSON data is invalid: {}", err.value); });  
      });  
  });  
```  
  
Here are the semantics:  
  
1. The return type of `handle_more` is an opaque type that captures the handler types and a "return type" which can be provided explicitly as the sole template argument *or* will be deduced as the common type of the return types of the handlers (If another nested handler returns another `handle_more`, the common type will "unwrap" the `handle_more`-return-type for that handler).  
2. The `context` deduction helpers recognize if a handler returns `handle_more`, and will recurse into its nested handlers to find additional types that it needs to create slots for.  
3. The nested handler is invoked directly within the `handle_more` evaluation.  
4. The return values from `handle_more` will propagate out: So the `handle_more` handlers need to have a return value that is convertible to the overall return type of the `try_handle_{some,all}`.  
  
Here's the implementation details:  
  
1. The return type of `handle_more` is `leaf_detail::more_handlers_result<R, H...>`, where `R` is the common result type and `H...` are the inner handlers.  
2. The machinery of `handle_more` is in `handler_caller`, which will recognize a handler that returns a `more_handlers_result`.  
3. The best way I can initially think to get the slots tuple down into `handle_more` was by using a thread-local pointer to an abstract class with a `virtual R invoke(H&&...)` (`more_handler_invoker_base<R, H...>`). The `handler_caller` creates a concrete instance that stores the slots and `error_info`, and then stores the thread-local pointer-to-base for that invoker. `handle_more` then loads that pointer and calls `invoke(hs...)`, which then does the actual `handle_error_(tup, ei, hs...)`. This trickery with thread-local pointers feels a bit hairy, though...?  
4. The `handler_caller` will return the *actual* return value of the handler by unwrapping it from the `more_handlers_result` object.  
  
Open issues with the current implementation:  
  
1. In the above example, the handler for `e_project_path` will be invoked if that slot is loaded, but it is possible that none of the nested handlers would be satisfied. Currently `handle_more` requires an "everything" handler at the end (like with `try_handle_all`), but it would be better if it recognized whether it was being called for `try_handle_all` or `try_handle_some` before requiring a catch-all handler or not.  
2. The thread-local pointer is loaded before the handler containing the `handle_more` is invoked, and only loaded when `handle_more` is invoked later on. In a pathological case, some code in between those two points could change the pointer and this will explode in `handle_more` :slightly_smiling_face:.

---

## Comment 1

> Username: zajo  
> Created_at: 2022-01-08 23:12:08 UTC  
> Url: https://github.com/boostorg/leaf/pull/40#issuecomment-1008176059  

This is interesting, but maybe I'm missing part of the motivation for it. Would this approach not be sufficient for your use case:  
  
```  
leaf::try_catch(  
  [] { return open_project (); },  
  [](e_project_path path, e_missing_file const * missing, e_json_string const * json_str, e_json_parse_error const * err_parse, e_json_schema_error const * err_schema) {  
    log("Error while opening project in {}", path.value);  
    if( missing )  
        log("Missing file: {}", missing->value);  
    if( json_str ) {  
        log("Error in JSON '{}'", json_str->value);  
        if( err_parse )  
            log("Invalid JSON: {}", err_parse->value);  
        if( err_schema )  
            log("JSON data is invalid: {}", err_schema->value)  
    }  
  });  
```

---

## Comment 2

> Username: vector-of-bool  
> Created_at: 2022-01-16 06:49:07 UTC  
> Url: https://github.com/boostorg/leaf/pull/40#issuecomment-1013821413  

Sorry for the delay. Got busy just after I pushed this.  
  
---  
  
In general, yes, using pointer parameters allows similar behavior for many cases, but the same could be said for the utility of having multiple handlers at the top level. It is effectively putting the responsibility of handler dispatch back on the user, which defeats some of the declarative nature of the API.  
  
---  
  
I just pushed another set of changes that fixes Issue [1] in my prior comment. That is: Now when dispatching to a handler, the check for handler viability now additionally consults the return type of the handler: One of the handlers in a `more_handlers_result<>` must be viable for the current error, otherwise the enclosing handler is not viable. This applies recursively.  
  
This changes things up a bit, since now pointer parameters can no longer emulate the feature:  
  
```c++  
int r = leaf::try_handle_all(  
    []() -> leaf::result<int>  
    {  
        return leaf::new_error(info<1>{}, info<3>{});  
    },  
    [](info<1>) {  // [h1]  
        return leaf::handle_more([](info<2>) { return 2; });  
    },  
    [](info<1>) {  // [h2]  
        return leaf::handle_more([](info<3>) { return 3; });  
    },  
    [] { return -1; }  
);  
```  
  
With this fix, only handler `[h2]` will be viable with `info<1>` and `info<3>` loaded, as `[h1]` requires `info<2>` due to the `handle_more` return value.

---

## Comment 3

> Username: zajo  
> Created_at: 2022-01-16 07:51:08 UTC  
> Url: https://github.com/boostorg/leaf/pull/40#issuecomment-1013828098  

I'll review tomorrow, but I have questions:  
  
1. What happens if we make this change:  
  
```  
int r = leaf::try_handle_all(  
    []() -> leaf::result<int>  
    {  
        return leaf::new_error(info<1>{}, info<3>{});  
    },  
    [](info<1>) {  // [h1]  
        return leaf::handle_more(  
            [](info<2>) { return 2; },  
            [] { return 3; }  
        );  
    },  
    [](info<1>) {  // [h2]  
        return leaf::handle_more([](info<3>) { return 3; });  
    },  
    [] { return -1; }  
);  
```  
  
Presumably h2 will never be considered, since h1 handles all errors?  
  
2. Does all this work correctly when handlers are captured with `std::make_tuple`? In other words, would this work as expected:  
  
```  
auto handlers = std::make_tuple(  
    [](info<1>) {  // [h1]  
        return leaf::handle_more([](info<2>) { return 2; });  
    },  
    [](info<1>) {  // [h2]  
        return leaf::handle_more([](info<3>) { return 3; });  
    },  
    [] { return -1; }  
);  
```  
  
and then:  
  
```  
int r = leaf::try_handle_all(  
    []() -> leaf::result<int>  
    {  
        return leaf::new_error(info<1>{}, info<3>{});  
    },  
    handlers );  
);  
```

---

## Comment 4

> Username: vector-of-bool  
> Created_at: 2022-01-17 23:10:30 UTC  
> Url: https://github.com/boostorg/leaf/pull/40#issuecomment-1014944371  

1. Yes, it will call `h1` because there is a `handle_more` handler that handles all cases after the `info<1>` is handled.  
  
2. It _should_, but I'll add some test cases to make sure.

---

## Comment 5

> Username: zajo  
> Created_at: 2022-01-18 02:58:08 UTC  
> Url: https://github.com/boostorg/leaf/pull/40#issuecomment-1015027086  

I'm concerned about making it even more difficult than it already is to know what handler will be called when. Without `handle_more`, at least we know we have to consider one level of handlers in the order they're listed, like we're used to do with `catch` statements. I have to think. It'd be more convincing if I found the pointer argument version I posted in any way inferior, but I don't think that it is: either way you have to read more code, and the pointer version has the benefit of that code being just plain C++, rather than template magic. For example, you can set a break point and step through to see what happens.  
  
Why don't you like the pointer argument version?

---
