# #110 - How to dynamic create a FieldLikeTuple [Closed]

> Username: nesc1  
> Created at: 2023-01-26 00:13:18 UTC  
> Updated at: 2023-01-28 13:04:47 UTC  
> Closed at: 2023-01-28 13:04:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/110  

Hi, my last code executes a list of dynamic params and used a std::vector for that on the old code.  
Now when I try to execute a statement execution with a vector it gives me a compilation error.  
  
My question is how do I create a dynamic FieldLikeTuple?  
  
I tried with std::tuple_cat with no success because i can't save the new tuple on the same variable...

---

## Comment 1

> Username: anarthal  
> Created at: 2023-01-26 00:54:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/110#issuecomment-1404423584  

Hi,  
  
You can't. `FieldLikeTuple`s are `std::tuple`s, which are static.  
  
I'd recommend you using [statement::start_execution](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__statement/start_execution.html), which accepts an arbitrary iterator range.  
  
If you're upgrading from 0.2.0 to master, a few things to note:  
* `resultset` is now plain in-memory data, rather than an I/O object. When you call `statement::execute`, you get a `resultset` by lvalue reference. This is equivalent to `execute` + `resultset::read_all` in 0.2.0.  
* `statement::start_execution` behave like the old `statement::execute`: they send the execution request but don't read the rows. They return an `execution_state` by lvalue reference. An `execution_state` is similar to the old `resultset` I/O object.  
* To read the rows, call `connection::read_one_row` or `connection::read_some_rows`. These functions return view objects, which are like `std::string_view`. You can take ownership of them using the `row` and `rows` classes.  
  
Example code:  
  
```  
// prepare the statement  
tcp_ssl_statement stmt;  
conn.prepare_statement("SELECT * FROM...", stmt);  
  
// execute it  
std::vector<field> params; // get it from wherever  
execution_state st;  
stmt.start_execution(params.begin(), params.end(), st);  
  
// read rows  
while (!st.complete()) {  
    rows_view batch = conn.read_some_rows(st);  
    // batch will contain an indeterminate number of rows. Use it however you want  
}  
```  
  
I wasn't expecting a lot of interest in the iterator version of statement execution, so I only implemented it as `start_execution`, and not as `execute`. Would you mind answering a couple questions about your use case, so I can direct my implementation efforts?  
* Does your statement generate any rows? (i.e. is it a SELECT vs an INSERT).  
* If it does, are you reading them one by one (i.e. with the old `resultset::read_one`) or all of them at once (i.e.with the old `resultset::read_all`).  
* What is the requirement that prevents you from knowing the number of parameters at compile time?  
  
I've also seen that the new docs have -4 of SEO and aren't shown on google searches, so I'll include a link here:  
https://www.boost.org/doc/libs/master/libs/mysql/doc/html/index.html  
  
If you have any further question or request regarding migration, I'll be happy to help.  
  
Thanks for using the library!

---

## Comment 2

> Username: nesc1  
> Created at: 2023-01-26 10:37:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/110#issuecomment-1404820693  

Hi @anarthal thank you once more for the detailed answer.  
  
Then I will try the iterator version, I come up with a solution yesterday for the FieldLikeTuple way but I really don't like it, is based on a static detection of the size of the vector and build the tuple according to that.  
  
Trying to answer to your questions the best way I can:  
  
**> Does your statement generate any rows? (i.e. is it a SELECT vs an INSERT).**  
yes it normally generates, because normally I have conditions to filter the select, example: getMyUsers(bool filterBySex, std::optional<std::string> country.... etc)  
  
**> If it does, are you reading them one by one (i.e. with the old resultset::read_one) or all of them at once (i.e.with the old resultset::read_all).**  
yes I'm reading all, the old resultset::read_all yes  
  
**>What is the requirement that prevents you from knowing the number of parameters at compile time?**  
is like I said, normally is for using filters in my query's, more generic filters less parameters, more specific filters more parameters..  
  
(thank you for the link, yes is not easy to find the documentation no, thank you)  
  
Thank you again for providing the library,  
Best regards,  
Nuno

---

## Comment 3

> Username: anarthal  
> Created at: 2023-01-26 12:47:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/110#issuecomment-1404957880  

Hi @nesc1,  
  
Thank you for your answers, they will guide me. One more question, how are you handling these filters? Do you have a single different prepared statement for each possible combination of filters, or are you building the prepared statement SQL dynamically depending on whether the fields are present or not?  
  
With the data I have, I'd say that the option I presented you yesterday is the best one. You will have to read rows in batches as I showed you. I've opened #111 to implement the equivalent to `FieldLikeTuple`s, but with iterators.  
  
Regards,  
Ruben.

---

## Comment 4

> Username: nesc1  
> Created at: 2023-01-26 15:13:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/110#issuecomment-1405159724  

Hi @anarthal   
  
No, for dynamic queries I never use prepared statements, I use prepared statements but not dynamically.  
Yes normally I build SQL dynamically depending on whether the fields are present or not, yes.  
  
Yes I will test that code suggestion for my case, thank you.  
Regards,  
Nuno

---

## Comment 5

> Username: anarthal  
> Created at: 2023-01-28 13:04:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/110#issuecomment-1407394798  

Thanks for the info. Closing this as we have #111 to track the feature request.
