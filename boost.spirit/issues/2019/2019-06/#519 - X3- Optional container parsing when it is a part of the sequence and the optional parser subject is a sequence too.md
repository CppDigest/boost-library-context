# #519 - X3: Optional container parsing when it is a part of the sequence and the optional parser subject is a sequence too [Closed]

> Username: pulver  
> Created at: 2019-06-23 00:16:47 UTC  
> Updated at: 2021-04-24 00:45:10 UTC  
> Closed at: 2021-04-24 00:45:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/519  

In Boost 1.70.0, starting with `example/x3/employee.cpp` and applying the diff:  
```  
diff --git a/employee.cpp b/test.cpp  
index 6257102..cb0fdbb 100644  
--- a/employee.cpp  
+++ b/test.cpp  
@@ -31,7 +31,7 @@ namespace client { namespace ast  
     {  
         int age;  
         std::string forename;  
-        std::string surname;  
+        boost::optional<std::string> surname;  
         double salary;  
     };  
   
@@ -71,7 +71,7 @@ namespace client  
             >> '{'  
             >>  int_ >> ','  
             >>  quoted_string >> ','  
-            >>  quoted_string >> ','  
+            >>  -quoted_string >> ','  
             >>  double_  
             >>  '}'  
             ;  
@@ -118,7 +118,6 @@ main()  
   
             std::cout << "-------------------------\n";  
             std::cout << "Parsing succeeded\n";  
-            std::cout << "got: " << emp << std::endl;  
             std::cout << "\n-------------------------\n";  
         }  
         else  
```  
results in compiler errors, including  
```  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:194:24: error: no matching function for call to ‘move_to(std::remove_reference<const char&>::type, std::__cxx11::basic_string<char>&, boost::mpl::identity<boost::spirit::x3::traits::container_attribute>::type)’  
```  
In contrast, the following diff does successfully compile:  
```  
diff --git a/employee.cpp b/test.cpp  
index 6257102..79f35a0 100644  
--- a/employee.cpp  
+++ b/test.cpp  
@@ -32,7 +32,7 @@ namespace client { namespace ast  
         int age;  
         std::string forename;  
         std::string surname;  
-        double salary;  
+        boost::optional<double> salary;  
     };  
   
     using boost::fusion::operator<<;  
@@ -72,7 +72,7 @@ namespace client  
             >>  int_ >> ','  
             >>  quoted_string >> ','  
             >>  quoted_string >> ','  
-            >>  double_  
+            >>  -double_  
             >>  '}'  
             ;  
   
@@ -118,7 +118,6 @@ main()  
   
             std::cout << "-------------------------\n";  
             std::cout << "Parsing succeeded\n";  
-            std::cout << "got: " << emp << std::endl;  
             std::cout << "\n-------------------------\n";  
         }  
         else  
```  
  
### Summary  
The `optional` operator unary minus (`-`) works as expected when parsing a single `double_` into a `boost::optional<double>` member variable, but does not work as expected when parsing the compound parser `quoted_string` into a corresponding `boost::optional<std::string>`.

---

## Comment 1

> Username: pulver  
> Created at: 2019-06-23 00:43:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/519#issuecomment-504709095  

Issue resolved. A `string` is a sequence/vector of characters, so when an optional operator `-` is applied, it collapses to the sequence. In other words, remove the `boost::optional` and test to see if it is empty to see if there was a match or not.

---

## Comment 2

> Username: djowel  
> Created at: 2019-06-23 23:01:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/519#issuecomment-504795245  

> Issue resolved. A `string` is a sequence/vector of characters, so when an optional operator `-` is applied, it collapses to the sequence. In other words, remove the `boost::optional` and test to see if it is empty to see if there was a match or not.  
  
Yes, but I still want to investigate if this is a bug or not. The optional and use of - seems like a valid use-case, at first glance.

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-06-07 21:39:12 UTC  
> Updated at: 2021-04-19 13:35:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/519#issuecomment-640283821  

It is definitely a bug.  
  
I recently observed the root issue, but have not come to a solution. The issue is about handling container attributes in sequence && tuple attribute partitioning in sequence && passing thru unary a partitioned tuple attribute. In more details: tuple partitioning creates an iterator range of single element and it is not unwrapped because it will be passed to another sequence parser, and it is something that `parse_into_container_impl` relies on currently.  
  
MWE:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/std_pair.hpp>  
  
int main()  
{  
    using boost::spirit::x3::char_;  
    using boost::spirit::x3::int_;  
    char const* const it = "123:abc";  
    std::pair<int, boost::optional<std::string>> v;  
    parse(it, it, int_ >> -(':' >> +char_), v);  
}  
```
