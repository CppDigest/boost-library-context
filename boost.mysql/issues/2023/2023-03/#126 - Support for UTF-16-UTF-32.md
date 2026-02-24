# #126 - Support for UTF-16/UTF-32 [Closed]

> Username: H1X4Dev  
> Created at: 2023-03-05 00:27:53 UTC  
> Updated at: 2023-03-12 18:42:15 UTC  
> Closed at: 2023-03-12 18:42:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/126  

Hello,  
  
By any chance would you be able to include support for `std::wstring` as well?  
  
Thanks!

---

## Comment 1

> Username: anarthal  
> Created at: 2023-03-05 00:31:26 UTC  
> Updated at: 2023-03-05 00:31:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1454937054  

It's difficult to include it in the `field` interface, but it is possible to add it when we implement #60, which would allow you to read rows into structs directy.  
  
What's your use case? Which would be your ideal interface?  
  
Regards,  
Ruben.

---

## Comment 2

> Username: H1X4Dev  
> Created at: 2023-03-05 00:59:55 UTC  
> Updated at: 2023-03-05 10:38:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1454943947  

Well, it's mainly because our entire application is already using UTF-16 since we work with EUC-KR encoding.   
Specifying a define such as `BOOST_MYSQL_USE_UTF16` to simply swap `std::string` to `std::wstring` would be ideal.  
  
Edit: Seems like I had a little oversight and on UNIX platforms `sizeof(wchar_t)` is 4 bytes, so possibility to switch for `std::u16string` or `std::u32string` would be the best. I have modified the issue title accordingly.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-03-06 09:21:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1455769711  

Hi,  
  
From what I've seen, EUC-KR is an encoding taking from 1 to 2 bytes per character. For example, this string contains two characters and each of them takes two bytes:  
  
```  
완     성  
BF CF BC BA  
```  
  
While other characters (e.g. the ones in ASCII) only take one:  
```  
a  b  c  
61 62 63  
```  
  
In other words, as far as I understand, EUC-KR is not UTF-16. So, a couple questions:  
* Which character set are you using to read results from the database? I.e. are you issuing a `SET NAMES "euckr"` or a similar method to tell MySQL to send results in EUC-KR?  
* How are you using the results that you retrieve from the database? Do you intend to pass them to the Windows native APIs (taking `wchar_t` strings)?  
* How are you encoding your query strings? The ones you use when calling `connection::query()` or `connection::prepare_statement()`.  
* Are you using metadata strings? For example, `metadata::column_name()`.  
* Are you using server diagnostics? I.e. `diagnostics::server_message()`.  
  
Thanks,  
Ruben.

---

## Comment 4

> Username: H1X4Dev  
> Created at: 2023-03-06 19:11:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1456797606  

To answer the questions directly:  
* Q: Which character set are you using to read results from the database? I.e. are you issuing a SET NAMES "euckr" or a similar method to tell MySQL to send results in EUC-KR?  
  * A: We are using `utf16_unicode_ci`. We are not issuing SET NAMES command. Upon testing the results are correctly represented in `std::string`.  
* Q: How are you using the results that you retrieve from the database? Do you intend to pass them to the Windows native APIs (taking wchar_t strings)?  
  * The results retrieved from database get encoded into a packet that gets sent over to client for handling. Yes we are passing to Windows native API using `wchar_t`.  
* How are you encoding your query strings? The ones you use when calling connection::query() or connection::prepare_statement().  
  * Currently we are just using [utfcpp](https://github.com/nemtrif/utfcpp) to convert `std::u16string` to `std::string` for `std::make_tuple` and using the same library to convert back to the format that we need, which seemingly worked fine at cost of performance.  
* Are you using metadata strings? For example, metadata::column_name().  
  * We have not explored this yet.  
* Are you using server diagnostics? I.e. diagnostics::server_message().  
  * Yes.  
  
But whole TDLR is: Yes, it works fine with `std::string`, it is just the matter of skipping the conversion process if we can process the data directly as `std::u16string` or `std::wstring`, current workaround is mentioned previously.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-03-07 11:23:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1457999000  

Hi,  
  
Where are you setting `utf16_unicode_ci`? Setting at at the table or column level (e.g. '`CREATE TABLE t (f TEXT) COLLATE utf16_general_ci`) or the table level (`CREATE TABLE (f1 TEXT COLLATE utf16_general_ci`) won't affect to what you receive as a client at all. MySQL has the concept of "connection character set and collation", which determine the encoding of the strings you send and receive from the server. The most reliable to change this is via `SET NAMES <encoding>`. By default, Boost.MySQL uses `utf8mb4_general_ci`, so this is the encoding you're actually using right now.  
  
The obvious thing to do (`SET NAMES utf16`) doesn't work because of a MySQL limitation. Some multibyte character sets can't be used like this, [as per the MySQL docs](https://dev.mysql.com/doc/refman/8.0/en/charset-connection.html#charset-connection-impermissible-client-charset).  
  
This doesn't mean that all is lost. When you do `SET NAMES <charset>`, you're actually setting several system variables that control the encoding of things sent to and received from the client. The relevant vars are:  
- `character_set_client`, which controls what you send to the client. This one can't be UTF16.  
- `character_set_results`, which controls what MySQL sends to you. You can set this by using `SET SESSION character_set_results = 'utf16'`.  
  
Before doing anything, please read [this section of the docs](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/charsets.html) explaining which variables affect what, since you're entering a potentially dangerous path down here. Things like error messages will also get affected by the character set change.  
  
If you do that, you will start receiving UTF-16 encoded strings. They will, however, be represented as single byte `string_view`/`std::string`, but they will contain the byte representation of UTF-16 strings. To be able to use them, you need to make a byte copy into a `std::wstring`. For example:  
  
```  
row_view my_row = /* obtain a row by invoking query() or execute_statement() */  
string_view utf16_string = my_row.at(0).as_string(); // This points to a UTF-16 string  
std::wstring usable_string (utf16_string.size() / 2, 0); // We will copy the UTF-16 characters here  
std::memcpy(usable_string.data(), utf16_string.data(), utf16_string.size()); // This requires C++17  
// Now you can use usable_string normally  
```  
  
Beware of byte ordering: this is assuming that whatever MySQL sent to you matches the byte order of the machine you're running. By default, MySQL uses UTF-16 big endian. You've got `utf16le` in case you need it.  
  
Remember that you can't just `reinterpret_cast<const wchar_t*>(utf16_string.data())` because of strict aliasing and alignment issues.  
  
**TL; DR**  
==========  
- Trying MySQL to send UTF-16 can be problematic, measure well before going down this path. You may want to consider using [multi-function operations](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/multi_function.html) to reduce copying, instead of this.  
- Because of MySQL limitations, you will need some conversions. Anything you send (query strings and statement parameters) can't be UTF-16.  
- IIRC Windows now allows UTF-8 in its APIs, although the support is quite recent. Depending on your application, you may also consider using [Boost.Nowide](https://github.com/boostorg/nowide), which can make converting UTF8 <=> UTF16 in the context of the Windows API smoother.  
  
Please let me know what path do you take. If you end up settings `character_set_results` to UTF16, I can consider implementing a solution to avoid this terrible `memcpy` thing. The limitation on `character_set_client` can't be lifted, since it's server-side.  
  
Thanks,  
Ruben.

---

## Comment 6

> Username: anarthal  
> Created at: 2023-03-12 15:20:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1465226282  

Hi @H1X4Dev, which solution did you finally employ?  
  
Thanks,  
Ruben.

---

## Comment 7

> Username: H1X4Dev  
> Created at: 2023-03-12 18:42:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/126#issuecomment-1465270537  

Hello,  
  
Unfortunately it was decided to switch to utf-8 database and simply converting u16string using the library that I have mentioned previously. While the solution is ugly, we want to ensure the compatibility for our software since it the server will be deployed on Linux and the Client will be on Windows. The client software is rather old, so the compatibility is a must, even if we have to use a hacky way to achieve the goal.  
  
I will close the issue in hopes of #60 being able to achieve the set goal.  
  
Thanks.
