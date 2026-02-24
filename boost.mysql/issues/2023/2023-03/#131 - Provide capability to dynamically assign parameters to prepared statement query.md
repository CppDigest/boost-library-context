# #131 - Provide capability to dynamically assign parameters to prepared statement query [Closed]

> Username: Xirema  
> Created at: 2023-03-24 18:15:28 UTC  
> Updated at: 2023-04-03 11:17:03 UTC  
> Closed at: 2023-04-01 20:55:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/131  

While I enjoy the syntax of using tuples to assign parameter values to a query, I am quickly finding that creates code bloat when dealing with queries that have any degree of customization.  
  
To show what I mean, here's an example query that I'm in the process of updating in my application. This function was originally written to use the MYSQL Connector C++ library.  
  
```  
	mysql::results getLookup(  
		mysql::tcp_ssl_connection& connection,  
		int64_t modId,  
		std::optional<std::string_view> nameFilter = {}  
	) {  
		std::string sql = R"SQL(  
			select   
				T.*   
			from   
				DATA.TVALS T  
			where   
				T.MOD_ID = ?  
		)SQL";  
		if (nameFilter) {  
			sql += R"SQL(  
			and T.NAME = ?  
			)SQL";  
		}  
  
		auto statement = connection.prepare_statement(sql);  
		mysql::results results;  
		if (nameFilter) {  
			connection.execute_statement(statement, std::make_tuple(modId, *nameFilter), results);  
		}  
		else {  
			connection.execute_statement(statement, std::make_tuple(modId), results);  
		}  
		return results;  
	}  
```  
  
So the problem I'm running into is the handling of this filter: in order to execute the statement with the name filter, I have to construct an entirely new tuple, appending the appropriate parameters as needed.  
  
This gets especially more complicated if, for example, my SQL has multiple conditions to filter on, which might or might not be present. Consider this function header:  
  
```  
	mysql::results getReferenceLookup(  
		mysql::tcp_ssl_connection& session,  
		int64_t modId,   
		std::optional<std::string_view> ownerNameFilter = {},  
		std::optional<std::string_view> effectReferenceFilter = {},  
		std::optional<std::string_view> sortColumn = {}  
	)  
```  
In order to handle this case, I'll need to write eight unique cases, one for each of the permutations of which filters are or aren't present on the query. This is what that code currently looks like, which is obviously undesirable.  
  
```  
		auto statement = connection.prepare_statement(sql);  
		mysql::results results;  
		if (ownerNameFilter) {  
			if (effectReferenceFilter) {  
				if (sortColumn) {  
					connection.execute_statement(statement, std::make_tuple(modId, *ownerNameFilter, *effectReferenceFilter, *sortColumn), results);  
				}  
				else {  
					connection.execute_statement(statement, std::make_tuple(modId, *ownerNameFilter, *effectReferenceFilter), results);  
				}  
			}  
			else {  
				if (sortColumn) {  
					connection.execute_statement(statement, std::make_tuple(modId, *ownerNameFilter, *sortColumn), results);  
				}  
				else {  
					connection.execute_statement(statement, std::make_tuple(modId, *ownerNameFilter), results);  
				}  
			}  
		}  
		else {  
			if (effectReferenceFilter) {  
				if (sortColumn) {  
					connection.execute_statement(statement, std::make_tuple(modId, *effectReferenceFilter, *sortColumn), results);  
				}  
				else {  
					connection.execute_statement(statement, std::make_tuple(modId, *effectReferenceFilter), results);  
				}  
			}  
			else {  
				if (sortColumn) {  
					connection.execute_statement(statement, std::make_tuple(modId, *sortColumn), results);  
				}  
				else {  
					connection.execute_statement(statement, std::make_tuple(modId), results);  
				}  
			}  
		}  
		return results;  
```  
  
What I would like is instead to have some kind of syntax using a `parameter_pack` or similar construct that would allow me to dynamically assign parameters, like so:  
  
```  
	mysql::results getLookup(  
		mysql::tcp_ssl_connection& connection,  
		int64_t modId,  
		std::optional<std::string_view> nameFilter = {}  
	) {  
		std::string sql = R"SQL(  
			select   
				T.*   
			from   
				DATA.TVALS T  
			where   
				T.MOD_ID = ?  
		)SQL";  
		mysql::proposed::parameter_pack parameters; //vector-like  
		parameters.push_back(modId);  
		if (nameFilter) {  
			sql += R"SQL(  
			and T.NAME = ?  
			)SQL";  
			parameters.push_back(*nameFilter);  
		}  
  
		auto statement = connection.prepare_statement(sql);  
		mysql::results results;  
		connection.execute_statement(statement, parameterPack, results);  
		return results;  
	}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2023-03-25 18:25:28 UTC  
> Updated at: 2023-03-25 18:26:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/131#issuecomment-1483890914  

Hi @Xirema,  
  
What you're looking for is [the iterator overload of prepared statement execution](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/start_statement_execution/overload4.html). This is available in Boost 1.82, but it's experimental and not mentioned (yet) in the discussion, only in the reference.  
  
This version of statement execution is only available as a multi-function operation (yet, see #111). This means that, instead of calling `connection::execute_statement`, you must use `connection::start_statement_execution` and then read rows with `connection::read_some_rows`. Please read [this section on multi-function operations](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/multi_function.html) before proceeding, since they are slightly more complex than plain `execute_statement`.  
  
Some example code:  
```  
void getLookup(  
	mysql::tcp_ssl_connection& connection,  
	int64_t modId,  
	std::optional<std::string_view> nameFilter = {}  
) {  
	std::string sql = R"SQL(  
		select   
			T.*   
		from   
			DATA.TVALS T  
		where   
			T.MOD_ID = ?  
	)SQL";  
	std::vector<mysql::field_view> parameters;  
	parameters.emplace_back(modId);  
	if (nameFilter) {  
		sql += R"SQL(  
		and T.NAME = ?  
		)SQL";  
		parameters.emplace_back(*nameFilter);  
	}  
  
	auto statement = connection.prepare_statement(sql);  
	mysql::execution_state st;  
	connection.start_statement_execution(statement, parameterPack, st);  
	while (!st.complete())  
	{  
		mysql::rows_view batch = connection.read_some_rows(st);  
		// Use batch  
	}  
}  
```  
  
Some notes:  
* `batch` points into the connection's network buffer, once you call `read_some_rows` again, it will dangle. You can take ownership of it using `mysql::rows`. You can't, however, append to `rows`.   
* Once you call `start_statement_execution`, you **must** read all rows until they run out. Otherwise, you will get packet mismatches and bad things will happen.  
  
We expect #111 to go into Boost 1.83.  
  
Please let me know what solution you end up using, and any trouble you may encounter, since that will help me improve the lib.  
  
Cheers,  
Ruben.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-03-30 22:53:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/131#issuecomment-1491068776  

Hi @Xirema,  
  
What implementation path did you finally choose?  
  
Regards,  
Ruben.

---

## Comment 3

> Username: Xirema  
> Created at: 2023-04-01 20:18:28 UTC  
> Updated at: 2023-04-01 20:22:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/131#issuecomment-1493106582  

@anarthal   
  
So for the purposes of getting my code reasonable to maintain, I ended up just converting code over to use the `start_statement_execution`. There are a few "workarounds" I ended up having to use to keep the code clean, though.   
  
```  
	using Results = std::vector<mysql::row>;  
	using ParameterPack = std::vector<mysql::field>;  
	Results getResults(  
		mysql::tcp_ssl_connection& connection,  
		mysql::execution_state& state  
	) {  
		Results results;  
		while (!state.complete()) {  
			auto rows = connection.read_some_rows(state);  
			for (auto const& row : rows) {  
				results.emplace_back(row);  
			}  
		}  
		return results;  
	}  
  
	std::vector<mysql::field_view> asView(ParameterPack const& parameters) {  
		std::vector<mysql::field_view> ret;  
		for (auto const& field : parameters) {  
			ret.emplace_back(field);  
		}  
		return ret;  
	}  
  
	Results getReferenceLookup(  
		mysql::tcp_ssl_connection& connection,  
		int64_t modId,   
		std::optional<std::string_view> ownerNameFilter = {},  
		std::optional<std::string_view> effectReferenceFilter = {}  
	) {  
		std::string sql = R"SQL(  
			select   
				T.*   
			from   
				DATA.REFERENCETABLE T  
			where   
				T.MOD_ID = ?  
		)SQL";  
		ParameterPack parameters;  
		parameters.emplace_back(modId);  
		if (ownerNameFilter) {  
			sql += R"SQL(  
			and T.REFERENCECOLUMN = ?  
			)SQL";  
			parameters.emplace_back(*ownerNameFilter);  
		}  
		if (effectReferenceFilter) {  
			sql += R"SQL(  
			and T.EFFECT_TYPE = ?  
			)SQL";  
			parameters.emplace_back(*effectReferenceFilter);  
		}  
  
		auto statement = connection.prepare_statement(sql);  
		auto parametersView = asView(parameters);  
		mysql::execution_state state;  
		connection.start_statement_execution(  
			statement,  
			parametersView.begin(),  
			parametersView.end(),  
			state  
		);  
		  
		return getResults(connection, state);  
	}  
```  
  
So the multi-function operation worked. However, it does expose that it would be really nice if we, as users of the library, could mutate the `mysql::results` object ourselves, since I wasn't able to find a way to create the object myself by filling it with results from the multi-function operation, and I ended up resorting to just simulating it with a vector.  
  
And naturally, I agree with the suggestion in #111 that an overload of `execute_statement` that just takes the iterators would also be valuable.  
  
The other issue I ran into is that the iterators of `start_execution_statement` refused to implicitly convert `mysql::field` into `mysql::field_view`, so I had to add a helper method that would fix that. This was MSVC 17.3.6, so maybe that's an issue with compiler compatibility, but it would still be good to look at whether or not the template is written correctly, since it *seems like* it should just accept a vector of fields.

---

## Comment 4

> Username: anarthal  
> Created at: 2023-04-01 20:55:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/131#issuecomment-1493117467  

Hi @Xirema,  
  
You don't need the `asView` function - `start_statement_execution` should be able to work with a range of `field`s, too.  
  
I can confirm that #111 will go into Boost 1.83, which should make your code much cleaner. I will close this issue.  
  
Kind regards,  
Ruben.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-04-03 11:17:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/131#issuecomment-1494136804  

Alternatively, you may rewrite your query and use the following:  
  
```  
	mysql::results getReferenceLookup(  
		mysql::tcp_ssl_connection& connection,  
		int64_t modId,   
		std::optional<std::string_view> ownerNameFilter = {},  
		std::optional<std::string_view> effectReferenceFilter = {}  
	) {  
		const char* sql = R"SQL(  
			SELECT T.*  
			FROM DATA.REFERENCETABLE T  
			WHERE T.MOD_ID = ?  
			AND ISNULL(?) OR T.REFERENCECOLUMN = ?  
			AND ISNULL(?) OR T.EFFECT_TYPE = ?  
		)SQL";  
		mysql::results result;  
		auto stmt = conn.prepare_statement(sql);  
		mysql::field_view ownerName = ownerNameFilter ? mysql::field_view(*ownerNameFilter) : mysql::field_view(nullptr);  
		mysql::field_view effectReference = effectReferenceFilter ? mysql::field_view(*effectReferenceFilter) : mysql::field_view(nullptr);  
		connection.execute_statement(  
			stmt,  
			std::make_tuple(modId, ownerName, ownerName, effectReference, effectReference)  
			result  
		);  
		return result;  
	}  
```  
  
You may want to do some more clever logic with `IF` in the `SELECT` clause, but bear in mind that this will likely inhibit the use of indexes.
