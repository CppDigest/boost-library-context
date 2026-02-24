# #51 - Creating prepared statements dinamically [Closed]

> Username: Nevyn75  
> Created at: 2021-05-31 10:34:26 UTC  
> Updated at: 2021-06-04 14:14:01 UTC  
> Closed at: 2021-06-02 06:22:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/51  

Hi.  
I'd like to know if it's possible to create prepared statements dynamically, for example by taking values from an external JSON files.  
  
My problem is that make_values creates a std::array which size is fixed on the number of the make_values parameters, so it seems not possible to add values in runtime...  
  
Is there a solution, please?  
  
Thank you.

---

## Comment 1

> Username: anarthal  
> Created at: 2021-06-02 06:22:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/51#issuecomment-852770103  

Hi @Nevyn75,  
  
Yes, this is supported. You don't need to use `make_values`, as this function is intended as a utility when you need to create a `std::array<boost::mysql::value>` from plain literals. You can still call `prepared_statement::execute` passing in a dynamic collection of `boost::mysql::value`, like `std::vector` or `std::list`.  
  
For example:  
  
```  
std::vector<boost::mysql::value> my_params;  
// Read params from your JSON file and fill my_params  
// You can create boost::mysql::value instances from strings, ints...  
// Example: my_params.push_back(boost::mysql::value("this_is_a_test"));  
boost::mysql::tcp_prepared_statement stmt = // prepare your statement  
stmt.execute(my_params);  
```  
[This](https://anarthal.github.io/mysql/mysql/ref/boost__mysql__prepared_statement/execute.html) is the complete reference of the `execute` function you will be using. As you can see, it takes a [ValueCollection](https://anarthal.github.io/mysql/mysql/ref/boost__mysql__ValueCollection.html) as argument. You can find details on those links about the exact set of types you can pass. But any standard ordered collection having `boost::mysql::value` as `value_type` should do.  
  
Let me know if that solves your problem.  
  
Cheers,  
Ruben.

---

## Comment 2

> Username: Nevyn75  
> Created at: 2021-06-04 14:14:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/51#issuecomment-854759840  

Fantastic!  
It works like a charm :-)  
  
Thank you.
