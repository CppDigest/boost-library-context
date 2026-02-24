# #41 - Inline parameter descriptions yield strange results [Closed]

> Username: anarthal  
> Created at: 2020-07-05 09:18:43 UTC  
> Updated at: 2020-11-10 00:50:16 UTC  
> Closed at: 2020-11-10 00:50:15 UTC  
> Url: https://github.com/boostorg/docca/issues/41  

These comments:  
  
```  
/// Initializing constructor  
connection_params(  
        boost::string_view username, ///< Username  
        boost::string_view password, ///< Possibly empty password  
        boost::string_view db = "",   ///< Database to use, or empty string for no database.  
        collation connection_col = collation::utf8_general_ci, ///< The default character set and collation for the connection.  
        const ssl_options& opts = ssl_options() ///< The TLS options to use with this connection.  
    )  
```  
Yield this:  
  
![printscreen](https://user-images.githubusercontent.com/34971811/86529480-df65df00-bea8-11ea-9d11-b7a5c3d39f84.png)  
  
Workaround: use \param (that works fine)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-10 00:50:15 UTC  
> Url: https://github.com/boostorg/docca/issues/41#issuecomment-724376319  

A javadoc on each parameter is not a feature of doxygen
