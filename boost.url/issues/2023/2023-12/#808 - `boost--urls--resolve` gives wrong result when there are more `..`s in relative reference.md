# #808 - `boost::urls::resolve` gives wrong result when there are more `..`s in relative reference [Closed]

> Username: jhcarl0814  
> Created at: 2023-12-25 19:24:10 UTC  
> Updated at: 2024-01-03 20:55:06 UTC  
> Closed at: 2024-01-03 20:55:05 UTC  
> Url: https://github.com/boostorg/url/issues/808  

https://www.rfc-editor.org/rfc/rfc3986  
  
# code:  
```  
#include <QUrl>  
#include <boost/url.hpp>  
  
int main(int argc, char *argv[])  
{  
    for (QUrl a1 : {  
             QUrl("scheme:a/b/c"),  
             QUrl("scheme:a/b/c/"),  
             QUrl("scheme:/a/b/c"),  
             QUrl("scheme:/a/b/c/"),  
         })  
    {  
        for (QUrl a2 : {QUrl("../../../.."), QUrl("../../../../")})  
        {  
            qDebug() << a1 << a1.scheme() << a1.userName() << a1.password() << a1.host() << a1.port() << a1.path() << a1.query() << a1.fragment();  
  
            qDebug() << a2 << a2.scheme() << a2.userName() << a2.password() << a2.host() << a2.port() << a2.path() << a2.query() << a2.fragment();  
  
            QUrl a3 = a1.resolved(a2);  
            qDebug() << a3 << a3.scheme() << a3.userName() << a3.password() << a3.host() << a3.port() << a3.path() << a3.query() << a3.fragment();  
            // a3 = a3.adjusted(QUrl::UrlFormattingOption::NormalizePathSegments);  
            // qDebug() << a3 << a3.scheme() << a3.userName() << a3.password() << a3.host() << a3.port() << a3.path() << a3.query() << a3.fragment();  
  
            qDebug() << "";  
        }  
    }  
    for (boost::system::result<boost::urls::url_view> a1 : {  
             boost::urls::parse_uri(boost::core::string_view("scheme:a/b/c")),  
             boost::urls::parse_uri(boost::core::string_view("scheme:a/b/c/")),  
             boost::urls::parse_uri(boost::core::string_view("scheme:/a/b/c")),  
             boost::urls::parse_uri(boost::core::string_view("scheme:/a/b/c/")),  
         })  
    {  
        for (boost::system::result<boost::urls::url_view> a2 : {boost::urls::parse_relative_ref(boost::core::string_view("../../../..")), boost::urls::parse_relative_ref(boost::core::string_view("../../../../"))})  
        {  
            qDebug() << q_has_char8_t::QUtf8StringView(a1->buffer()) << q_has_char8_t::QUtf8StringView(a1->scheme()) << a1->user() << a1->password() << a1->host() << q_has_char8_t::QUtf8StringView(a1->port()) << a1->path() << a1->query() << a1->fragment();  
  
            qDebug() << q_has_char8_t::QUtf8StringView(a2->buffer()) << q_has_char8_t::QUtf8StringView(a2->scheme()) << a2->user() << a2->password() << a2->host() << q_has_char8_t::QUtf8StringView(a2->port()) << a2->path() << a2->query() << a2->fragment();  
  
            boost::urls::url a3;  
            boost::urls::resolve(a1.value(), a2.value(), a3);  
            qDebug() << q_has_char8_t::QUtf8StringView(a3.buffer()) << q_has_char8_t::QUtf8StringView(a3.scheme()) << a3.user() << a3.password() << a3.host() << q_has_char8_t::QUtf8StringView(a3.port()) << a3.path() << a3.query() << a3.fragment();  
            // a3.normalize();  
            // qDebug() << q_has_char8_t::QUtf8StringView(a3.buffer()) << q_has_char8_t::QUtf8StringView(a3.scheme()) << a3.user() << a3.password() << a3.host() << q_has_char8_t::QUtf8StringView(a3.port()) << a3.path() << a3.query() << a3.fragment();  
  
            qDebug() << "";  
        }  
    }  
}  
```  
  
# output:  
```  
QUrl("scheme:a/b/c") "scheme" "" "" "" -1 "a/b/c" "" ""  
QUrl("../../../..") "" "" "" "" -1 "../../../.." "" ""  
QUrl("scheme:..") "scheme" "" "" "" -1 ".." "" ""  
  
QUrl("scheme:a/b/c") "scheme" "" "" "" -1 "a/b/c" "" ""  
QUrl("../../../../") "" "" "" "" -1 "../../../../" "" ""  
QUrl("scheme:../") "scheme" "" "" "" -1 "../" "" ""  
  
QUrl("scheme:a/b/c/") "scheme" "" "" "" -1 "a/b/c/" "" ""  
QUrl("../../../..") "" "" "" "" -1 "../../../.." "" ""  
QUrl("scheme:..") "scheme" "" "" "" -1 ".." "" ""  
  
QUrl("scheme:a/b/c/") "scheme" "" "" "" -1 "a/b/c/" "" ""  
QUrl("../../../../") "" "" "" "" -1 "../../../../" "" ""  
QUrl("scheme:%00") "scheme" "" "" "" -1 "\u0000" "" ""  
  
QUrl("scheme:/a/b/c") "scheme" "" "" "" -1 "/a/b/c" "" ""  
QUrl("../../../..") "" "" "" "" -1 "../../../.." "" ""  
QUrl("scheme:/") "scheme" "" "" "" -1 "/" "" ""  
  
QUrl("scheme:/a/b/c") "scheme" "" "" "" -1 "/a/b/c" "" ""  
QUrl("../../../../") "" "" "" "" -1 "../../../../" "" ""  
QUrl("scheme:/") "scheme" "" "" "" -1 "/" "" ""  
  
QUrl("scheme:/a/b/c/") "scheme" "" "" "" -1 "/a/b/c/" "" ""  
QUrl("../../../..") "" "" "" "" -1 "../../../.." "" ""  
QUrl("scheme:/") "scheme" "" "" "" -1 "/" "" ""  
  
QUrl("scheme:/a/b/c/") "scheme" "" "" "" -1 "/a/b/c/" "" ""  
QUrl("../../../../") "" "" "" "" -1 "../../../../" "" ""  
QUrl("scheme:/") "scheme" "" "" "" -1 "/" "" ""  
  
"scheme:a/b/c" "scheme" "" "" "" "" "a/b/c" "" ""  
"../../../.." "" "" "" "" "" "../../../.." "" ""  
"scheme:" "scheme" "" "" "" "" "" "" ""  
  
"scheme:a/b/c" "scheme" "" "" "" "" "a/b/c" "" ""  
"../../../../" "" "" "" "" "" "../../../../" "" ""  
"scheme:" "scheme" "" "" "" "" "" "" ""  
  
"scheme:a/b/c/" "scheme" "" "" "" "" "a/b/c/" "" ""  
"../../../.." "" "" "" "" "" "../../../.." "" ""  
"scheme:.." "scheme" "" "" "" "" ".." "" ""  
  
"scheme:a/b/c/" "scheme" "" "" "" "" "a/b/c/" "" ""  
"../../../../" "" "" "" "" "" "../../../../" "" ""  
"scheme:../" "scheme" "" "" "" "" "../" "" ""  
  
"scheme:/a/b/c" "scheme" "" "" "" "" "/a/b/c" "" ""  
"../../../.." "" "" "" "" "" "../../../.." "" ""  
"scheme:/" "scheme" "" "" "" "" "/" "" ""  
  
"scheme:/a/b/c" "scheme" "" "" "" "" "/a/b/c" "" ""  
"../../../../" "" "" "" "" "" "../../../../" "" ""  
"scheme:/../../" "scheme" "" "" "" "" "/../../" "" ""  
  
"scheme:/a/b/c/" "scheme" "" "" "" "" "/a/b/c/" "" ""  
"../../../.." "" "" "" "" "" "../../../.." "" ""  
"scheme:/.." "scheme" "" "" "" "" "/.." "" ""  
  
"scheme:/a/b/c/" "scheme" "" "" "" "" "/a/b/c/" "" ""  
"../../../../" "" "" "" "" "" "../../../../" "" ""  
"scheme:/../" "scheme" "" "" "" "" "/../" "" ""  
```  
  
# expected output:  
result url should be `scheme:/`, result's path should be `/`

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-12-28 20:23:47 UTC  
> Url: https://github.com/boostorg/url/issues/808#issuecomment-1871467790  

That's confusing. What is/are the input, the current output, and the expected output?

---

## Comment 2

> Username: jhcarl0814  
> Created at: 2023-12-28 21:07:33 UTC  
> Updated at: 2023-12-28 21:12:11 UTC  
> Url: https://github.com/boostorg/url/issues/808#issuecomment-1871489931  

@alandefreitas See also discussions in https://bugreports.qt.io/browse/QTBUG-120396  
`resolve("scheme:a/b/c", "../../../..")` current output: `"scheme:"` expected output: `"scheme:/"`  
`resolve("scheme:a/b/c", "../../../../")` current output: `"scheme:"` expected output: `"scheme:/"`  
`resolve("scheme:a/b/c/", "../../../..")` current output: `"scheme:.."` expected output: `"scheme:/"`  
`resolve("scheme:a/b/c/", "../../../../")` current output: `"scheme:../"` expected output: `"scheme:/"`  
(`resolve("scheme:/a/b/c", "../../../..")` current output: `"scheme:/"` expected output: `"scheme:/"`)  
`resolve("scheme:/a/b/c", "../../../../")` current output: `"scheme:/../../"` expected output: `"scheme:/"`  
`resolve("scheme:/a/b/c/", "../../../..")` current output: `"scheme:/.."` expected output: `"scheme:/"`  
`resolve("scheme:/a/b/c/", "../../../../")` current output: `"scheme:/../"` expected output: `"scheme:/"`  
because https://www.rfc-editor.org/rfc/rfc3986 says:  
```  
   2.  While the input buffer is not empty, loop as follows:  
  
       A.  If the input buffer begins with a prefix of "../" or "./",  
           then remove that prefix from the input buffer; otherwise,  
  
       B.  if the input buffer begins with a prefix of "/./" or "/.",  
           where "." is a complete path segment, then replace that  
           prefix with "/" in the input buffer; otherwise,  
  
       C.  if the input buffer begins with a prefix of "/../" or "/..",  
           where ".." is a complete path segment, then replace that  
           prefix with "/" in the input buffer and remove the last  
           segment and its preceding "/" (if any) from the output  
           buffer; otherwise,  
  
       D.  if the input buffer consists only of "." or "..", then remove  
           that from the input buffer; otherwise,  
  
       E.  move the first path segment in the input buffer to the end of  
           the output buffer, including the initial "/" character (if  
           any) and any subsequent characters up to, but not including,  
           the next "/" character or the end of the input buffer.  
```

---

## Comment 3

> Username: alandefreitas  
> Created at: 2023-12-28 21:28:49 UTC  
> Url: https://github.com/boostorg/url/issues/808#issuecomment-1871500780  

Thanks. I'll look into it.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-01-02 18:54:09 UTC  
> Url: https://github.com/boostorg/url/issues/808#issuecomment-1874417128  

> because https://www.rfc-editor.org/rfc/rfc3986 says:  
  
This has been obsoleted by [Errata 4547](https://www.rfc-editor.org/errata/eid4547).  
  
The expected output should include the unmatched "..". This means the current outputs are still wrong, but the expected outputs should include the ".." segments.

---

## Comment 5

> Username: jhcarl0814  
> Created at: 2024-01-02 23:03:41 UTC  
> Url: https://github.com/boostorg/url/issues/808#issuecomment-1874666747  

@alandefreitas Alright, it seems the example (for base URI having absolute path case) is conflicting with the algorithm now. I'd better wait until they figure out what they really want.

---

## Comment 6

> Username: jhcarl0814  
> Created at: 2024-01-03 03:42:48 UTC  
> Url: https://github.com/boostorg/url/issues/808#issuecomment-1874814493  

@alandefreitas Thanks for the effort! The expected outputs in the added unit tests are clear and make more sense now.
