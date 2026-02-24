# #213 Feature/69 query formatting [Closed]

> Username: anarthal  
> Created at: 2024-02-12 18:22:53 UTC  
> Updated at: 2024-03-15 18:55:45 UTC  
> Closed at: 2024-03-04 17:11:30 UTC  
> Url: https://github.com/boostorg/mysql/pull/213  

Adds a way to safely compose SQL query strings client-side. The notation is inspired in `fmtlib`.  
  
Documentation for this PR can be found [here](https://anarthal.github.io/sql-formatting/libs/mysql/doc/html/). The page describing this feature is [this](https://anarthal.github.io/sql-formatting/libs/mysql/doc/html/mysql/sql_formatting.html).

---

## Review 1 [Commented]

> Username: Hailios  
> Created_at: 2024-02-20 10:45:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mysql/pull/213#pullrequestreview-1890105761  

📁 example/snippets.cpp

```diff
1652 |+ //[sql_formatting_formatter_specialization
1653 |+ // We want to add formatting support for employee_t
1654 |+ struct employee_t
```

> Username: Hailios  
> Created_at: 2024-02-20 10:45:09 UTC  
> Updated_at: 2024-02-20 10:45:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1495607366  

you might want to be consistent with _t suffixes on types.   
I know its difficult to be consistent.   
there is a similar employee struct in batch_insert.cpp which doesn't have the "_t" suffix

> Username: anarthal  
> Created_at: 2024-03-15 18:35:51 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1526686751  

Caused by https://github.com/boostorg/mysql/issues/237 - will fix when solving the issue.


---

## Review 2 [Commented]

> Username: Hailios  
> Created_at: 2024-02-20 11:15:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mysql/pull/213#pullrequestreview-1890118842  

📁 example/batch_inserts.cpp

```diff
 116 |+             emp.company_id,
 117 |+             emp.salary
 118 |+         );
```

> Username: Hailios  
> Created_at: 2024-02-20 10:51:41 UTC  
> Updated_at: 2024-02-20 11:15:12 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1495616168  

somewhere here might be good to point out that one could specialize boost::mysql::formatter for the type as an alternative. (link to the docs)  
  
I'm refering to this doc section  
````  
// We want to add formatting support for employee_t  
struct employee_t  
{  
    std::string first_name;  
    std::string last_name;  
    std::string company_id;  
};  
  
namespace boost {  
namespace mysql {  
  
template <>  
struct formatter<employee_t>  
{  
    // formatter<T> should define, at least, a function with signature:  
    //    static void format(const T&, format_context_base&)  
    // This function must use format_sql_to, format_context_base::append_raw  
    // or format_context_base::append_value to format the passed value.  
    // We will make this suitable for INSERT statements  
    static void format(const employee_t& emp, format_context_base& ctx)  
    {  
        format_sql_to(ctx, "{}, {}, {}", emp.first_name, emp.last_name, emp.company_id);  
    }  
};  
  
}  // namespace mysql  
}  // namespace boost


📁 example/snippets.cpp

```diff
2175 |+             // We're trying to format a double infinity value, which is not
2176 |+             // supported by MySQL. This will throw an exception.
2177 |+             format_sql(opts, "SELECT {}", HUGE_VAL);
```

> Username: Hailios  
> Created_at: 2024-02-20 11:06:25 UTC  
> Updated_at: 2024-02-20 11:15:12 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1495634850  

this snippet, although meant to show exceptions, but it could at least try to assign the returned string to indicate that format_sql is meant to return a string.  
  
in-particular it shows the difference compared to `format_sql_to`


📁 doc/qbk/23_sql_formatting_advanced.qbk

```diff
 136 |+     Passing an incorrect `format_options` value to formatting functions may cause
 137 |+     escaping to generate incorrect values, which may generate vulnerabilities.
 138 |+     Stay safe and always use [refmem any_connection format_opts] instead of
```

> Username: Hailios  
> Created_at: 2024-02-20 11:12:16 UTC  
> Updated_at: 2024-02-20 11:15:12 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1495641561  

it seems like following this advice will not save the user from the issue stated just above regarding that at some points the client (which I assume includes the connection) doesn't know what the correct character set is.

> Username: anarthal  
> Created_at: 2024-03-15 18:55:45 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1526705974  

It does because `any_connection::format_opts` will contain an error instead of a value if the character set can't be determined accurately. I've included a more detailed description in this section.

---

📁 doc/qbk/23_sql_formatting_advanced.qbk

```diff
 157 |+ [heading Re-using string memory]
 158 |+ 
 159 |+ You can pass a string value to the context's constructor, to re-use memory:
```

> Username: Hailios  
> Created_at: 2024-02-20 11:15:04 UTC  
> Updated_at: 2024-02-20 11:15:12 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#discussion_r1495645796  

maybe mention how the value is being used so that the user can get a mental model of how to structure a reuse scenario.  
  
for example, is the input captured by reference, and used. or maybe is it moved into the constructor, and then moved out when calling `.get() `.


---

## Comment 3

> Username: anarthal  
> Created_at: 2024-03-05 11:02:38 UTC  
> Url: https://github.com/boostorg/mysql/pull/213#issuecomment-1978501920  

Completed via squash-merge (5e8992ba880284e505404404de907316c2683337)

---
