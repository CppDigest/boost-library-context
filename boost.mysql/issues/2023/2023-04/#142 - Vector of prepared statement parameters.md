# #142 - Vector of prepared statement parameters [Closed]

> Username: tmayoff  
> Created at: 2023-04-20 17:06:32 UTC  
> Updated at: 2023-04-21 18:45:16 UTC  
> Closed at: 2023-04-21 18:45:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/142  

I'm trying to create a query builder that dynamically adds parameters to a the list of prepared statements (something I have working using the mariadbcppconnector) I'd like to do the same with this library, is there a way to pass parameters with a non-compile time known number of them

---

## Comment 1

> Username: anarthal  
> Created at: 2023-04-21 12:27:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/142#issuecomment-1517759832  

Yes, you can. You need to use the iterator version of [`start_statement_execution`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/start_statement_execution.html). Using this, you can pass any forward range of `field_view`s as parameters.  
  
Right now, this forces you to use the [multi-function interface](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/multi_function.html) - i.e., there is no `execute_statement` that takes an iterator pair and returns a `results`.  
  
Example code:  
  
```  
// conn is a connection object, stmt is a statement object  
  
// Obtain a collection containing the parameters of your statement as field_view's.  
// field_view's are lightweight variant-like objects that can represent any MySQL type  
std::vector<field_view> params { field_view(42), field_view("abc"), field_view(nullptr) };  
  
// an execution_state is an object that holds info about an execute operation,  
// and is the main interface to multi-function operations  
execution_state st;  
  
// start the operation. This sends the request to the server and reads the response, but not the rows  
conn.start_statement_execution(stmt, params.begin(), params.end(), st);  
  
// Read the rows  
while (!st.complete())  
{  
    // Read a batch. This batch points into conn's buffers and is valid  
    // until you perform any further network activity on conn  
    rows_view row_batch = conn.read_some_rows(st);  
  
    // Use the batch as required  
}  
```  
  
There is no way to coalesce several `rows_views` into a single `results` object.  
  
Boost 1.83 will include support for executing statements with iterator ranges and returning a `result` - the feature is available for preview in the develop branch. If it's something you're interested in, let me know and I'll provide more documentation on it.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: tmayoff  
> Created at: 2023-04-21 13:38:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/142#issuecomment-1517849188  

I would be interested in trying out what's in the develop branch, some more documentation would be great thanks!

---

## Comment 3

> Username: tmayoff  
> Created at: 2023-04-21 15:12:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/142#issuecomment-1517983592  

I managed to find the documentation myself, and figure it out. Thanks for the help!

---

## Comment 4

> Username: anarthal  
> Created at: 2023-04-21 18:45:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/142#issuecomment-1518213252  

As an additional note, in develop you may also use `field`s to execute statements.`field` is like `field_view`, but it owns strings and blobs. So something like  
  
```  
vector<field> params;  
results result;  
conn.execute(stmt.bind(params.begin(), params.end()), result);  
```  
  
Will work.  
  
Note that develop is not as stable as official releases, though.  
  
Regards,  
Ruben.
