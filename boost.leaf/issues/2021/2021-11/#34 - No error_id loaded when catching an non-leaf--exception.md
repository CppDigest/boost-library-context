# #34 - No error_id loaded when catching an non-leaf::exception [Closed]

> Username: vector-of-bool  
> Created at: 2021-11-22 02:52:52 UTC  
> Updated at: 2021-12-14 18:24:33 UTC  
> Closed at: 2021-12-13 23:09:05 UTC  
> Url: https://github.com/boostorg/leaf/issues/34  

This one took a while to track down, but I have narrowed it down to this small snippet. This is admittedly convoluted in appearance, but I was able to run into this behavior on my own with a much more spread-out code.  
  
Here are two test cases that exhibit two related (but unexpected(?)) behaviors:  
  
## Error Object is Dropped:  
  
```c++  
auto fun = [] {  
    return leaf::try_catch([]() -> std::pair<int, int> {  
        auto augment = leaf::on_error(info2{1729});  // [1]  
        leaf::try_catch(  
            [] {   
                throw my_exception(12);  
            },  
            [](const my_exception& e) {  
                leaf::current_error().load(info1{42});  // [2]  
                throw;  
            });  
        // unreachable  
    }, [](const my_exception& e, info1* v1, info2* v2) {  
        // Return the pair of {info1, info2}  
        return std::make_pair(v1 ? v1->value : -1, v2 ? v2->value : -1);  
    });  
};  
auto pair = fun();  
BOOST_TEST_EQ(pair.first, 42);  
BOOST_TEST_EQ(pair.second, 1729);  
pair = fun();  
BOOST_TEST_EQ(pair.first, 42);  
BOOST_TEST_EQ(pair.second, 1729);  
```  
  
In this case  
  
1. the bare `throw my_exception` _does not_ initialize a new `error_id` in the current thread.  
2. The handler will now try to `.load()` into the current in-flight error at `[2]`.  
    - Since there is no new `error_id` in flight, it will attach `info1` to whatever `error_id` just happened to be loaded in the current thread (Possibly just throwing the `info` away).  
3. The exception is then re-thrown with a bare `throw;`. Still, no new `error_id` is generated.  
4. The `augment` object's destructor at `[1]` will detect a new exception in-flight, but also detect that no new `error_id` has been created. It will then call `new_error()` (via `error_monitor::check_id()`) and attach an `info2` to that error. The value of `info1` is now inaccessible to the intended handler immediately below.  
  
Additional quirk: If one moves the `on_error` object into the innermost throwing-lambda expression, then it's destructor will call `new_error()` (as expected!) before the exception is caught and this code will work.  
  
## Result differences:  
  
```c++  
auto fun = [](bool use_leaf_exception) {  
    return leaf::try_catch([&]() -> std::pair<int, int> {  
        auto augment = leaf::on_error(info2{1729}); // [1]  
        leaf::try_catch(  
            [&] {   
                if (use_leaf_exception) {  
                    throw leaf::exception(my_exception(12));  
                } else {  
                    throw my_exception(12);  
                }  
            },  
            [](const my_exception& e) {  
                leaf::current_error().load(info1{42});  // [2]  
                throw;  
            });  
        // unreachable  
    }, [](const my_exception& e, info1* v1, info2* v2) {  
        // Return the pair of {info1, info2}  
        return std::make_pair(v1 ? v1->value : -1, v2 ? v2->value : -1);  
    });  
};  
auto pair = fun(false);  
BOOST_TEST_EQ(pair.first, 42);  
BOOST_TEST_EQ(pair.second, 1729);  
pair = fun(true);  
BOOST_TEST_EQ(pair.first, 42);  
BOOST_TEST_EQ(pair.second, 1729);  
```  
  
As a side effect of the prior quirk, the result will differ depending on whether `leaf::exception()` is called. In this case, if `use_leaf_exception` is `true`, then the correct result appears, otherwise it will fail

---

## Comment 1

> Username: zajo  
> Created at: 2021-11-22 03:17:50 UTC  
> Updated at: 2021-11-22 03:27:28 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-975033894  

You probably have a complete program, can you put it here or maybe on Godbolt? I'll take a closer look to confirm things work as intended, but at first glance I don't see a bug: `current_error` is documented as "returns the error ID from the last time `new_error` was called", which may very well have been handled already.  
  
The correct technique is described here: https://www.boost.org/doc/libs/release/libs/leaf/doc/html/index.html#tutorial-interoperability. Essentially, you should use `error_monitor`, or better yet -- just use `on_error`.  
  
If you think that there is a better way to support this use case, I'm all ears.

---

## Comment 2

> Username: vector-of-bool  
> Created at: 2021-11-22 23:10:59 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-975995813  

It is correct that this behaves as documented, but I feel like there's an unexpected interaction between disparate parts. I can't say that the behavior is necessarily "wrong", but that it felt counterintuitive once I actually understood why code was behaving as it was.  
  
The actual program itself is fairly large and WIP, but I've tweaked my code to behave "as expected."  
  
I suppose it may be better to show more closely what I was _trying_ to do, and why I was very confused initially:  
  
```c++  
void do_stuff() {  
  auto augment = on_error(e_some_data{data});  
  return try_catch(  
    [] { return do_stuff_inner(); }, // Might throw  
    [] (catch_<some_exception> e) {  
      current_error().load(e_some_extra_info{e.matched.something});  
      throw;  
    });  
}  
```  
  
If `do_stuff_inner` exits with a non-`leaf::exception`-wrapped `some_exception` thrown, then the `current_error().load()` will load into the thread's prior error. The `augment` object will then call `new_error` because there is a new exception in-flight without a new `error_id` having been set for the thread, thus throwing away the `e_some_extra_info`. My intention is that `e_some_extra_info` is _only_ loaded when a `some_exception` is thrown, but otherwise not loaded.  
  
If, however, the `augment` object is omitted, then the "correct" behavior is seen because `new_error()` will not be called (although this is admittedly suspicious because `current_error()` will return some arbitrary prior `error_id` that is possibly stale).  
  
On the other hand, if the `do_stuff_inner()` throws with `leaf::exception`, then `new_error()` will have been called and everything behaves correctly. This is completely non-suspicious and is the ideal situation.  
  
As a final note, if the code is changed as such:  
  
```c++  
void do_stuff() {  
  return try_catch(  
    [] {  
      auto augment = on_error(e_some_data{data});  
      return do_stuff_inner();   
    },  
    [] (catch_<some_exception> e) {  
      current_error().load(e_some_extra_info{e.matched.something});  
      throw;  
    });  
}  
```  
  
(That is: `augment` occurs inside of the TryBlock), then `new_error` will be called as the exception is on its way out of the block and before `catch_` occurs, and thus again shows the "correct" behavior. In actuality, it might be the case that there is an `on_error` somewhere inside of `do_stuff_inner()`, so it can't be immediately known whether `new_error()` will be called before the `catch_`, unless you have total knowledge of the control flow path inside of `do_stuff_inner()`.  
  
I suppose my final point is that: You cannot _locally_ reason about whether `new_error()` has been called, despite being in an error-handling scope.  
  
One possible "solution" to such a quirk would be to have `try_catch()` behave similarly to an `on_error`/`error_monitor` and call `new_error()` if-and-only-if it detects a new exception in-flight with no new `error_id` set for the thread. However, this could be a breaking change if much code is already written to assume otherwise.  
  
Thoughts?

---

## Comment 3

> Username: zajo  
> Created at: 2021-11-22 23:39:50 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976010039  

Does this behave the way you want? https://godbolt.org/z/Kj3e7dxzM

---

## Comment 4

> Username: vector-of-bool  
> Created at: 2021-11-23 00:18:04 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976032189  

That gets close, and I thought about that, but it presents another issue in that it unconditionally calls `new_error()` from within the handler. See: https://godbolt.org/z/afGPqrKT5: The `info<3>` from `do_stuff_inner` is lost because the `augment` in the `catch_` block calls `new_error()` again.

---

## Comment 5

> Username: zajo  
> Created at: 2021-11-23 01:08:11 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976080340  

Yeah. My example also breaks if you change it to throw using `leaf::exception`: https://godbolt.org/z/5n4jd8K64

---

## Comment 6

> Username: zajo  
> Created at: 2021-11-23 03:56:05 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976149670  

I don't like this but I wonder if this is the correct thing to do? Please confirm that it fixes this particular problem.  
  
https://github.com/boostorg/leaf/commit/e7f008994b456101719a53db629b4d354eaaf641  
  
What I'm doing is, in the `on_error` destructor, if there is a `std::current_exception`, check if it carries an `error_id` and only call `new_error` if it does not.

---

## Comment 7

> Username: vector-of-bool  
> Created at: 2021-11-23 05:14:24 UTC  
> Updated at: 2021-11-23 05:16:43 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976174346  

That will allow the in-handler `on_error` to work, but seems to have a strange side effect: If I change the bare `throw;` into a `throw leaf::exception(catch_.matched)`, now the outer `augment` for `e_info<1>` gets dropped and only the `e_info<2>` gets loaded.  
  
---  
  
I think I can boil down the question even simpler:  
  
```c++  
void foo() {  
  auto augment = on_error(info1{});  
  leaf::try_catch(  
    [] { do_stuff(); },  
    [] (catch_<something>) {  
      current_error().load(info2{});  
      throw;  
    });  
}  
```  
  
Given _only_ this snippet, is it reasonable to expect that both `info1` and `info2` will be loaded onto the same `error_id`? Or are we _required_ to know more about what `do_stuff()` might be doing?  
  
---  
  
Without any changes to `try_catch` or `on_error`, one might use a wrapping function to get a desired effect:  
  
```c++  
auto new_error_on_throw(auto fn) {  
  error_monitor mon;  
  try { return fn(); }  
  catch (...) { mon.check_id(); throw; }  
}  
```  
  
That is: Call `fn`, and if it exits via exception and there is no new `error_id` on the current thread, call `new_error` and re-`throw`:  
  
```c++  
void foo() {  
  auto augment = on_error(info1{});  
  leaf::try_catch(  
    [] { new_error_on_throw(do_stuff); },  
    [] (catch_<something>) {  
      current_error().load(info2{});  
      throw;  
    });  
}  
```

---

## Comment 8

> Username: vector-of-bool  
> Created at: 2021-11-23 05:48:48 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976184062  

If you change the definition of `do_stuff()` in `exception_on_error_test.cpp` to this:  
  
```c++  
template <class Thrower>  
void do_stuff( Thrower thrower )  
{  
  auto augment = leaf::on_error(e_info<1>{1});  
  return leaf::try_catch(  
    [&]  
    {  
        thrower();  
        BOOST_ERROR("thrower must throw");  
    },  
  
    [ ]( leaf::catch_<some_exception> e)  
    {  
        auto augment = leaf::on_error(e_info<2>{2});  
        throw leaf::exception(e.matched);  
    } );  
}  
```  
  
then you end up with a test failure:  
  
```  
leaf::verbose_diagnostic_info for Error ID = 5  
Exception dynamic type: boost::leaf::leaf_detail::exception<some_exception const>  
std::exception::what(): std::exception:  
[with Name = e_info<2>]: 2  
../test/exception_on_error_test.cpp(80): test 'r == 0' ('2' == '0') failed in function 'int main()'  
1 error detected.  
```  
  
The second test (which uses a non-`leaf::exception` throw) still passes, so I'm not even sure what's going on in this case.

---

## Comment 9

> Username: zajo  
> Created at: 2021-11-23 06:27:29 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-976197426  

The "solution" I was exploring is pretty bad, it is not a good idea to try/catch in on_error.   
  
Does this work for you? https://godbolt.org/z/1x7EzMP1G

---

## Comment 10

> Username: zajo  
> Created at: 2021-11-25 17:40:24 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-979381150  

Actually can I get more context? To me it doesn't make sense to want to augment only a particular kind of error with some info. The info is typically independent of the error, it is additive information applicable to any error.

---

## Comment 11

> Username: vector-of-bool  
> Created at: 2021-11-26 23:23:01 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-980466024  

I've also been thinking about this issue as well. Here's what it looked like originally, which has since been cleaned up somewhat:  
  
```c++  
widget foo::parse_widget_json(json_lib::json_data data) {  
  auto _ = on_error(e_parse_widget_json_data{data});  
  return try_catch(  
    [&] { return walk_json_as_widget(data); }  
    [] (const json_lib::walk_exception& exc) {  
      current_error().load(e_parse_widget_json_error{exc.what()});  
      throw;  
    });  
}  
```  
  
The idea here was that the `walk_exception` was kept as the active exception along with any inner error information in-flight, but when that particular error was thrown, to add a `e_parse_widget_json_error` object into the mix, because a `walk_exception` wouldn't be enough that an upper handler would understand the error (this is arguably untrue, because of the `e_parse_json_widget_data` object in the other scope can disambiguate the error).  
  
The issue I was seeing is that _sometimes_ the `walk_exception` was thrown (by my own code) with a `new_error` (or an `on_load` causing a `new_error`), but _sometimes_ the `json_lib` (which is unaware of LEAF) would just `throw walk_exception` without any LEAF-ism to invoke the `new_error`. e.g.:  
  
```c++  
void handle_data(json_data d) {  
  if (some_condition(d)) {  
    BOOST_LEAF_THROW_EXCEPTION(walk_exception{"Ouch"});  
  } else {  
    throw walk_exception{"Oof"};  // No new_error()  
  }  
}  
```  
  
(In reality, I could only control the true-branch, and the else-branch was part of the external library, but they both threw the same exception type.)  
  
So the issue boiled down to: When I reach the exception handler for `walk_exception`, I couldn't be sure whether or not `new_error` had ever been called.  
  
My (incorrect) expectation was that `leaf_detail::try_catch_` would end up invoking `new_error` on the case of a new exception appearing without a new error having by set (which is the case for `on_error`).  
  
---  
  
Here's a hack that I just thought of that can _ensure_ `new_error` is called:  
  
```c++  
void handle_data(json_data d) {  
  auto _ = on_error(std::monostate{});  
  if (some_condition(d)) {  
    BOOST_LEAF_THROW_EXCEPTION(walk_exception{"Ouch"});  
  } else {  
    throw walk_exception{"Oof"};  
  }  
}  
```  
  
Because `on_error` will call `new_error` during stack-unwinding-on-exception if-and-only-if the `current_error() == error_id_` (via `error_monitor`), it doesn't the work to ensure that `new_error()` is always called _only once_.  
  
With this you might be able to hack together a semi-useful abstraction:  
  
```c++  
struct [[nodiscard]] new_error_on_throw {  
  decltype(on_error(std::monostate{})) _ = on_error(std::monostate{});  
};  
  
void handle_data(json_data d) {  
  new_error_on_throw _;  
  if (some_condition(d)) {  
    BOOST_LEAF_THROW_EXCEPTION(walk_exception{"Ouch"});  
  } else {  
    throw walk_exception{"Oof"};  
  }  
}  
```

---

## Comment 12

> Username: zajo  
> Created at: 2021-11-27 03:21:52 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-980493857  

I don't understand why is it necessary to grab the `.what()` and instead communicate it as a `e_parse_widget_json_error`. The `.what()` is available when you handle the error later on, right?

---

## Comment 13

> Username: zajo  
> Created at: 2021-12-13 23:09:05 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-992993924  

@vector-of-bool I'm closing this but feel free to reopen it if you think I've missed something.

---

## Comment 14

> Username: vector-of-bool  
> Created at: 2021-12-14 00:27:37 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-993036439  

Sorry to go quiet on it. I was able to work around the behavior pretty easily, but I may revisit if I can think of a clean solution to the more general confusion.

---

## Comment 15

> Username: zajo  
> Created at: 2021-12-14 18:24:33 UTC  
> Url: https://github.com/boostorg/leaf/issues/34#issuecomment-993858551  

I'm curious about your workaround.
