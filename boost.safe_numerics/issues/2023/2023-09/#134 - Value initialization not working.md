# #134 - Value initialization not working [Open]

> Username: christianbrugger  
> Created at: 2023-09-19 10:37:33 UTC  
> Updated at: 2023-09-24 03:02:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/134  

I am not sure if technically a bug, but from the documentation and talks what I understood was that in my code I can basically replace `int` with `safe<int>` and it should compile and give the same result with checks.  
  
I get into problems related to default initialization.  
  
#### 1. Default Initialization  
```  
auto x = int {};       // zero initialized  
auto y= safe<int> {};  // uninitialized  
```  
See on [godbolt](https://godbolt.org/z/G8d9ehW3n).  
  
  
#### 2. Aggregate Initialization  
  
This is obviously related, and I want to mention it, as this is how it came to my attention.  
```  
struct Point {  
    int x;  
    int y;  
};  
const auto p = Point {};  // zero initialized  
```  
With `safe<>`  
```  
struct Point {  
    safe<int> x;  
    safe<int> y;  
};  
const auto p = Point {};  // uninitialized  
```  
See on [godbolt](https://godbolt.org/z/q966cPf1W)

---

## Comment 1

> Username: robertramey  
> Created at: 2023-09-19 17:49:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/134#issuecomment-1726222376  

> from the documentation and talks what I understood was that in my code I can basically replace int with safe<int> and it should compile and give the same result with checks.  
  
Yes.  this is the intent.  
  
The fact demonstrated in your example and a close reading of https://en.cppreference.com/w/cpp/language/default_initialization#Notes convince me that my original understanding of initialization of int and other primitive variables was wrong.  I had assumed that if initialization wasn't specified, it wasn't done.  Turns out that it depends on the circumstances.  If done, the value is zero.  FWIW - another case where C++ has been gratuitously complicated - thus making it much harder to write a correct program. Primitive types such as int are initialized by default while non-primitives are not. Since safe<int> is not a primitive type, it isn't initialized by default.  
  
 I'll have to look into this. I'll leave this issue open as a reminder.  
  
Note that this subject is touched upon by another issue here. issue #90  
  
In the interests of demonstrating program correctness, I would recommend explicitly initializing all variables.  Note that in order to properly initialize safe<int> and related types, you'll have to become familiar with safe_literal ... etc.

---

## Comment 2

> Username: christianbrugger  
> Created at: 2023-09-22 06:48:33 UTC  
> Updated at: 2023-09-24 03:02:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/134#issuecomment-1730892322  

Hello Robert, thank you for your reflection. I agree with you that c++ is extremely complicated in the area of initialization. It is definitely also beyond my understanding in its entirety. Reading your cited article, I also realized that my terminology was not fully correct.  
  
Here is my current understanding:  
  
### Default initialization:  
  
This is when no "initializer" is provided, ([default initialization](https://en.cppreference.com/w/cpp/language/default_initialization)).  
  
```c++  
int x;  
safe<int> x;  
```  
This leads to "indeterminate values" for "non-class variables". So `int` stays uninitialized.  
  
Here the library has the same behavior as using `int` and `safe<int>`. Confirmed here https://godbolt.org/z/8v8KP1eMj  
  
### Value-initialization  
  
This is when the initializer consisting of an empty pair of parentheses or braces ([value initialization.](https://en.cppreference.com/w/cpp/language/value_initialization)).  
  
```c++  
int x {};  
int x = int ();  
int x = int {};  
```  
Or when used in class members or constructors.  
```c++  
class Foo {  
    int x {};  
};  
class Bar {  
    int x;  
    Test() : x() {}  
};  
```  
In this case non-class variables are zero-initialized ([zero initialization](https://en.cppreference.com/w/cpp/language/zero_initialization)).  
  
Here the behavior of `safe<int>` is different and values are not initialized. In this cases `int` cannot be replaced with `safe<int>`.  
  
## Possible Implementations  
  
Clearly it would be best if `safe<int>` could match `int` in both behaviors.   
  
I looked into this in the last 2 days intensely. However I don't see a straight-forward implementation. I made a list of possible ideas and the tradeoff. Please see my conclusion at the end.  
  
### Option 1 - never initialize `m_t` by default - status quo  
  
The current implementation of `safe_base` has a default constructor that doesn't mention `m_t` leading always to uninitialized values. It also raises a possible error if uninitialized.  
```c++  
template<class Stored, ...>  
class safe_base {  
    Stored m_t;  
    ...  
};  
  
template<class Stored, Stored Min, Stored Max, class P, class E>  
constexpr inline /*explicit*/ safe_base<Stored, Min, Max, P, E>::safe_base(){  
    static_assert(  
        std::is_arithmetic<Stored>(),  
        "currently, safe numeric base types must currently be arithmetic types"  
    );  
    dispatch<E, safe_numerics_error::uninitialized_value>(      
    "safe values must be initialized"  
    );  
}  
```  
  
### Option 2 - value initialize `m_t` by default  
  
A straight forward change would be to value-initialize `m_t` in the constructor as following:  
```c++  
template<class Stored, Stored Min, Stored Max, class P, class E>  
constexpr inline /*explicit*/ safe_base<Stored, Min, Max, P, E>::safe_base()   
  :  m_t() {  
    static_assert(  
        std::is_arithmetic<Stored>(),  
        "currently, safe numeric base types must currently be arithmetic types"  
    );  
}  
```  
However the problem is that this would zero initialize `safe<int>` in both cases for default and value initialization. As in the case for class types the default constructor is called in both cases.  
  
If a user switches from `safe<int>` back to `int` this could lead to undefined behavior. Not good.  
  
### Option 3 - defaulted default constructor  
  
Now its also possible default the constructor letting the compiler fill in the behavior:  
  
```c++  
template<class Stored, ...>  
class safe_base {  
    Stored m_t;  
public:  
    constexpr safe_base() = default;  
    ...  
};  
```  
  
Now this is very interesting, as this now forwards the rules of initialization to all underlying types. In this case `safe<int>` would match the `int` behavior in both cases of value and default initialization ([implicitly defined default constructor](https://en.cppreference.com/w/cpp/language/default_constructor#Implicitly-defined_default_constructor)). I also confirmed it by my own test: https://godbolt.org/z/hcavEM75a  
  
While this is good, the problem here is that we can't issue an uninitialized-value error. And as of now I am not sure how we could detect this in any other way. The standard doesn't provide any facility to check if a value is uninitialized. Also I don't see how we could set any other flags and nesting types won't help us either.  
  
So also not really an option, except we find a way to issue such an error.  
  
### Option 4 - add a new `initialization_policy`  
  
In this option we could add another policy to `safe_int` as a template parameter and call it `initialization_policy`. Two options make sense to me:  
```c++  
struct require_initialization {};  
struct zero_initialized {};  
```  
The first `require_initialization` would use option 1, status quo, and force the user to initialize all variables with a value, effectively disabling value initialization. The argument could be that **value initialization** is unintuitive and should be avoided.  
  
And option `zero_initialized` would use option 2, and always zero initialize. Here the argument is that we avoid all uninitialized values  and by construction and also enable code that relies on value initiation without needing it to be changed.  
  
### Option 5 - ask the user to subclass `safe<T>` and support subclassing  
  
We could ask the user to subclass `safe<int>` and add the value initialization if he wants that.  
  
```c++  
using IntBase = boost::safe_numerics::safe<int>;  
  
class Int : public IntBase {  
    public:  
        using IntBase::IntBase;  
        constexpr Int() : IntBase {IntBase {0}} {}  
};  
```  
  
However at the moment, I don't think safe-numerics supports sub-classing, so we would need to support this.  I am not sure if good to allow it or even feasible.  
  
## Discussion Questions  
  
### Why would we even want value initialization?  
  
It is not completely clear that we need it, but there are few arguments:  
  
#### 1) If we don't support it, a code base that uses `int` cannot upgrade to `safe<int>` without potential changes. So types are not strictly interchangeable, as advertised.  
  
The good news here is that one could set the exception policy for **uninitialized_value** to **trap_exception**. And then fix all the places where value initialization is used, during compile time. We could even argue that this improves the code quality, as relying on value initialization is unintuitive and prone to interpretation errors.  
  
#### 2) There are many containers that rely on [**default insertion**](https://en.cppreference.com/w/cpp/named_req/DefaultInsertable#Notes). Which result in value initialization for the standard allocators.  Examples:  
```c++  
std::vector<int>::resize(std::size_t count);  // (1) values beyond the current size are default inserted  
std::vector<int>::push();                     // (2) default inserts a value  
```  
In this case one could move to the methods that provide an explicit value.  
```c++  
std::vector<int>::resize(std::size_t count, int {0});  
std::vector<int>::push(int {0});  
```  
Again we could argue that this makes the code more clear.  
  
#### 3) Value initialization is part of [aggregate initialization](https://en.cppreference.com/w/cpp/language/aggregate_initialization) e.g. used for arrays:  
```c++  
std::array<int, N> a {};                     // (3) zero initializes all values  
```  
If `N` is a template parameter this is quite tricky to work around, as even `std::array<int, N> a;` would trap if a uninitialized-value exception policy is set. And `array` unlike `vector` does not have any other constructors. The "simplest" workaround I found would be:  
```c++  
namespace detail {  
template <typename T, std::size_t...Is>  
constexpr std::array<T, sizeof...(Is)>  
make_array(const T& value, std::index_sequence<Is...>)  
{  
    return {{(static_cast<void>(Is), value)...}};  
}  
}  
  
template <std::size_t N, typename T>  
constexpr std::array<T, N> make_array(const T& value)  
{  
    return detail::make_array(value, std::make_index_sequence<N>());  
}  
  
std::array<Int, 10> a = make_array<10>(Int {0});  
```  
And I don't even want to think about nested arrays.  
  
Maybe we could say this is an isolated case and we won't support it.  And ask the user to use the workaround in the rare case, they want to use an array with variable sizes.  
  
#### 4) Value initialization might be used in some templates to initialize values to zero. I have seen this used to initialize counters or sizes, e.g in some `std::vector` implementations.  
  
Oftentimes the types of these counters are not template parameters, so its a non issue.   
  
And for the cases where they are, we could say that this is out of scope for `safe<int>`. One would need to trust the template, or it needs to be rewritten to support safe<int>.  
  
### Why do we not just zero initialize our `safe<int>`s all the time?  
  
Here I am thinking of the stepper-motor examples you presented in one of your talks.   
  
If one want to use `safe<int>` only for verification and then in the real implementation move to `int`, we cannot allow for a case, where moving back to `int` introduces undefined behavior. And this would precisely be the case, if we always zero initialize our variables, namely for default initialization. Then `safe<int> a; ++a;` would work, but moving to `int a; ++a;` would be undefined behavior.  
  
## Conclusion  
  
For now I am really not sure where to go with this.   
  
The project I want to introduce safe-numerics has about 20k lines of code. I will see how far I can go with the missing value initialization. I will enable the trap-exceptions for uninitialized values, so I get errors at compile time. This to me sounds like a feasible migration path. And except of array aggregate avoiding value initialization should be trivial and improve readability.  
  
My suggestion for now would be to documented these two things:  
1) Stating that safe-numerics does not support value initialization. Maybe with some explanation of its implementation challenges and rationale why its not really needed.  
2) That if one wants to migrate existing code one should enable uninitialized value exceptions and set it trap at compile time to catch all these cases and rewrite them.  
  
One other thing I noticed is that the `default_exception_policy` ignores uninitialized values. Why is that the case? Although I don't think its possible to change that now, maybe it would be good to introduce another one that raises an exception or traps in those cases.

---

## Comment 3

> Username: christianbrugger  
> Created at: 2023-09-22 07:27:08 UTC  
> Updated at: 2023-09-22 07:30:41 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/134#issuecomment-1730936344  

Now thinking a bit more and reading #90, I think we should at least change the `default_exception_policy` to include uninitialized value exception.   
  
As currently those are ignore and using `safe<int>` in existing code can silently introduce UB. I think this is very bad, especially given the goal of the library.  
  
Perfectly valid C++ code:  
```c++  
auto get_value_or_zero(std::optional<int> value) -> int {  
    if (value) {  
        return *value;  
    }  
    return {};  
}  
```  
  
Here `safe<int>` in its defaults configuration silently introduces UB:  
```c++  
using Int = boost::safe_numerics::safe<int>;  
  
auto get_value_or_zero_UB(std::optional<Int> value) -> Int {  
    if (value) {  
        return *value;  
    }  
    return {};  // UB  
}  
```  
  
Not only that, but this code produces no compile time warnings even with `-pedantic -Wall -Wextra -Wuninitialized`.  
And the uninitialized value is not even found with the clang memory sanitizer when we enable optimizations, UB is really biting us here. See https://godbolt.org/z/f8MYx93qr

---

## Comment 4

> Username: robertramey  
> Created at: 2023-09-23 19:21:55 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/134#issuecomment-1732392021  

Holly smokes.  A huge effort on your part !!!  Welcome to the world of Boost C++ library development.  I'll chew on this a couple of days.  Given the intensive and extensive efforts you've made, I'm very interested in getting these considerations  incorporated into the the library.  Looks like we'll have to investigate they issue: Is the a library for guaranteeing program correctness or is the goal to detect C++ errors.  Anyway - thanks again and I'll try to looking into this soon.
