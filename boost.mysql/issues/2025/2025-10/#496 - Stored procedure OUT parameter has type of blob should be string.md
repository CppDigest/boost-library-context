# #496 - Stored procedure OUT parameter has type of blob should be string [Closed]

> Username: gitSomething  
> Created at: 2025-10-27 01:52:25 UTC  
> Updated at: 2025-10-29 12:41:14 UTC  
> Closed at: 2025-10-29 12:41:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/496  

Hi!  
My boost ver is 1.88.  
If stored procedure has output parameter of any string type (char, varchar, tinytext, text ....) its interpreted by library as field_kind::blob

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-27 21:43:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/496#issuecomment-3453448423  

This is actually a bug in MySQL server. The server doesn't send the right flags, so there's nothing we can do in the client. It's documented here: https://www.boost.org/doc/libs/latest/libs/mysql/doc/html/mysql/multi_resultset.html#mysql.multi_resultset.output_parameters.  
  
I reported the bug and has been confirmed by the MySQL team: https://bugs.mysql.com/110427  
  
I think that the bug report is not public, so I'll paste the description here:  
  
When calling a stored procedure from a prepared statement, binding OUT  
params using ? markers, the PS_OUT_PARAMS resultset sent by the server  
contains incorrect metadata. The flags and character set fields are not  
correct.  
  
This causes parsing problems in connectors, like parsing unsigned  
integers as signed, or character strings as binary blobs.  
  
Disclaimer: I develop a community C++ MySQL connector (Boost.MySQL) and  
I encountered this problem while running my test suite.  
  
How to repeat:  
With mysql-connector-python v8.0.31:  
  
```  
import mysql.connector  
  
cnx = mysql.connector.connect(user='root', password='',  
database='mytest', ssl_disabled=True)  
cursor = cnx.cursor()  
cursor.execute('DROP PROCEDURE IF EXISTS sp_bug')  
cursor.execute('DROP PROCEDURE IF EXISTS sp_ok')  
cursor.execute('''  
    CREATE PROCEDURE sp_bug (OUT pout TEXT)  
    BEGIN  
        set pout = 'hola';  
    END  
''')  
cursor.execute('''  
    CREATE PROCEDURE sp_ok ()  
    BEGIN  
        DECLARE pout TEXT;  
        set pout = 'abc';  
        select pout;  
    END  
''')  
  
cnx = mysql.connector.connect(user='root', password='',  
database='mytest', ssl_disabled=True)  
cursor = cnx.cursor(prepared=True)  
cursor.execute('CALL sp_bug(?)', (None, ), multi=True)  
print(cursor.fetchall())  
  
cnx = mysql.connector.connect(user='root', password='',  
database='mytest', ssl_disabled=True)  
cursor = cnx.cursor(prepared=True)  
cursor.execute('CALL sp_ok()', (), multi=True)  
print(cursor.fetchall())  
```  
  
Prints:  
```  
[(bytearray(b'hola'),)]  
[('abc',)]  
```  
  
Expected: [('abc',)] for both cases.  
  
If you inspect network traffic with Wireshark, the metadata packet for  
sp_bug has charset number 63 (binary), when it should have a non-binary  
collation (255, aka utf8mb4_0900_ai_ci for me). The sp_ok metadata  
packet is fine.  
  
This same thing happens for flags - unsigned integers are not flagged  
properly.  
  
  
  
  
Suggested fix:  
Item_param::make_field (sql/item.cc) makes use of m_out_param_info, but  
some fields (including collation and flags) seem to not be set properly  
in sql/sp_head.cc, in sp_head::execute_procedure, just before the call  
to set_out_param_info().

---

## Comment 2

> Username: anarthal  
> Created at: 2025-10-27 21:44:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/496#issuecomment-3453450575  

TL;DR: you will need to use the blob accessors and use the contents as if they were strings.

---

## Comment 3

> Username: gitSomething  
> Created at: 2025-10-29 11:47:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/496#issuecomment-3461108390  

i see.  
thanx for explanation.
