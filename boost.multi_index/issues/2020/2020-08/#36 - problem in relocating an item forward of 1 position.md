# #36 - problem in relocating an item forward of 1 position [Closed]

> Username: crillion  
> Created at: 2020-08-24 20:21:42 UTC  
> Updated at: 2020-08-25 18:49:33 UTC  
> Closed at: 2020-08-25 18:49:33 UTC  
> Url: https://github.com/boostorg/multi_index/issues/36  

Hi,  
  
 I have tested the attached code with boost 1.74 compiled with msys2 / mingw-w64 / g++ 10.2 on windows 10.  
    I have an example  with a multi_index container of entities which represent table columns. Each table column has its id, name as fields, and a position as intrinsic sequence index.  
  Suppose I add to the container these columns : { col0, col1, col2 }. I have found that if I relocate one column backward, i.e. col1 from position 1 to position 0, it works; but, again starting with columns { col0, col1, col2 }, if I instead try to move col0 from position 0 to position 1 it doesn't work (columns remain the same)  
  Could this be fixed ?  
  
```  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/member.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/sequenced_index.hpp>  
#include <iostream>  
#include <string>  
  
struct column  
{  
	column(int id, const std::string& name) : id(id), name(name) {}  
	  
	int id;  
	std::string name;  
	  
	bool operator<(const column& c) const { return id < c.id; }  
};  
  
// tags  
struct id {};  
struct name {};  
struct pos {};  
  
std::ostream& operator<<(std::ostream& out, const column& c)  
{  
	out << c.id << " - " << c.name;  
	  
	return out;  
}  
  
using column_set = boost::multi_index_container<  
	column,  
	boost::multi_index::indexed_by<  
		  
		// index 0 : sort by less<int> on id  
		boost::multi_index::ordered_unique<  
			boost::multi_index::tag<id>,  
			boost::multi_index::member<  
				column,  
				int,  
				&column::id  
			>  
		>,  
		  
		// index 1 : sort by less<string> on name  
		boost::multi_index::ordered_unique<  
			boost::multi_index::tag<name>,  
			boost::multi_index::member<  
				column,   
				std::string,   
				&column::name  
			>  
		>,  
		  
		// index 2 : sequenced  
		boost::multi_index::sequenced<  
			boost::multi_index::tag<pos>  
		>  
	>  
>;  
  
void print_column(const column& c, const column_set::index<pos>::type& pos_index)  
{  
	// get a view to pos index  
	auto pos_it = pos_index.iterator_to(c);  
  
	std::cout << c << " | " << std::distance(begin(pos_index), pos_it) << "\n";  
}  
  
void print_out_by_id(const column_set& cs)  
{  
	// get a view to id index  
	const column_set::index<id>::type& id_index = cs.get<id>();  
	  
	// get a view to pos index  
	const column_set::index<pos>::type& pos_index = cs.get<pos>();  
	  
	// use id_index as a regular std::set  
	// pos_index will give the actual item pos  
	std::for_each(  
		begin(id_index),  
		end(id_index),  
		[&pos_index](const auto& column)  
		{ print_column(column, pos_index); });  
}  
  
void print_out_by_name(const column_set& cs)  
{  
	// get a view to name_index  
	const column_set::index<name>::type& name_index = cs.get<name>();  
	  
	// get a view to pos index  
	const column_set::index<pos>::type& pos_index = cs.get<pos>();  
	  
	// use name_index as a regular std::set  
	std::for_each(  
		begin(name_index),   
		end(name_index),  
		[&pos_index](const auto& column)  
		{ print_column(column, pos_index); });  
}  
  
void print_out_by_pos(const column_set& cs)  
{  
	// get a view to id index  
	const column_set::index<pos>::type& pos_index = cs.get<pos>();  
	  
	// use id_index as a regular std::set  
	std::for_each(  
		begin(pos_index),  
		end(pos_index),  
		[&pos_index](const auto& column)  
		{ print_column(column, pos_index); });  
}  
  
void test_decrement_pos()  
{  
	column c0 { 0, "name" };  
	column c1 { 1, "age" };  
	column c2 { 2, "city" };  
	  
	column_set cs { c2, c0, c1 };  
  
	std::cout << "---------------\n";  
	std::cout << "by pos :\n";  
	print_out_by_pos(cs);  
	  
	auto& col_name_index = cs.get<name>();  
	std::string col_name = "name";  
	auto col_name_it = col_name_index.find(col_name);	  
  
	auto old_col_pos_it = cs.project<pos>(col_name_it);  
  
	auto& col_pos_index = cs.get<pos>();  
	auto new_col_pos_it = begin(col_pos_index);  
	std::advance(new_col_pos_it, 0);  
	  
	col_pos_index.relocate(new_col_pos_it, old_col_pos_it);  
  
	std::cout << "---------------\n";  
  
	print_out_by_pos(cs);	  
}  
  
void test_increment_pos()  
{  
	column c0 { 0, "name" };  
	column c1 { 1, "age" };  
	column c2 { 2, "city" };  
	  
	column_set cs { c2, c0, c1 };  
  
	std::cout << "---------------\n";  
	std::cout << "by pos :\n";  
	print_out_by_pos(cs);  
	  
	auto& col_name_index = cs.get<name>();  
	std::string col_name = "city";  
	auto col_name_it = col_name_index.find(col_name);	  
  
	auto old_col_pos_it = cs.project<pos>(col_name_it);  
  
	auto& col_pos_index = cs.get<pos>();  
	auto new_col_pos_it = begin(col_pos_index);  
	std::advance(new_col_pos_it, 1);  
	  
	col_pos_index.relocate(new_col_pos_it, old_col_pos_it);  
  
	std::cout << "---------------\n";  
  
	print_out_by_pos(cs);	  
}  
  
int main()  
{  
	test_decrement_pos(); // ok, this works and columns are printed with changed position  
	test_increment_pos(); // this doesn't seem to work :(  
}  
```  
  
Thanks,  
  
  Marco

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-08-25 07:24:07 UTC  
> Url: https://github.com/boostorg/multi_index/issues/36#issuecomment-679852978  

`relocate(position, it)` puts the element pointed to by `it` *just before* `position`. Your code:  
```  
void test_increment_pos()  
{  
        ...  
	auto new_col_pos_it = begin(col_pos_index);  
	std::advance(new_col_pos_it, 1);  
	  
	col_pos_index.relocate(new_col_pos_it, old_col_pos_it);  
        ...  
}  
```  
moves the element pointed to by `old_col_pos_it` ("city", first position in the sequenced index) *just before* `new_col_pos_it`, which occupies the second position: that is, you're moving the first element just before the second element, or, in other words, you're not relocating it at all. What you want to do is:  
```  
void test_increment_pos()  
{  
        ...  
	auto new_col_pos_it = begin(col_pos_index);  
	std::advance(new_col_pos_it, 2);  
	  
	col_pos_index.relocate(new_col_pos_it, old_col_pos_it);  
        ...  
}  
```

---

## Comment 2

> Username: crillion  
> Created at: 2020-08-25 16:38:52 UTC  
> Url: https://github.com/boostorg/multi_index/issues/36#issuecomment-680138268  

ok, I've tested it and it works. I hadn't got that point. Thanks.
