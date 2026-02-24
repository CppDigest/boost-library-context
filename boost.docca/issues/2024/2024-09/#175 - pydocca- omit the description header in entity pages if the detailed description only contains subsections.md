# #175 - pydocca: omit the description header in entity pages if the detailed description only contains subsections [Closed]

> Username: anarthal  
> Created at: 2024-09-03 16:29:44 UTC  
> Updated at: 2024-10-03 10:51:12 UTC  
> Closed at: 2024-10-03 10:51:12 UTC  
> Url: https://github.com/boostorg/docca/issues/175  

The XSLT version of docca had [logic](https://github.com/boostorg/docca/blob/5f349f0c7b81efa6f26cd1014cc15f5addfbbc87/include/docca/base-stage1.xsl#L228-L231) to omit the description header in cases where the detailed description is present, but only contains parameter descriptions and subsections, effectively causing the Description section to be empty.  
  
For instance:  
  
```cpp  
/**  
     * \brief Returns the rows retrieved by the SQL query.  
     * \details  
     *  
     * \tparam I Resultset index. For operations returning more than one resultset, you can explicitly  
     * specify this parameter to obtain the rows contained in the i-th resultset. If left unspecified,  
     * rows for the first resultset are returned.  
     *  
     * \return Returns a read-only span of the `I`-th row type.  
     *  
     * \par Preconditions  
     * `this->has_value() == true`  
     *  
     * \par Exception safety  
     * No-throw guarantee.  
     *  
     * \par Object lifetimes  
     * This function returns a view object, with reference semantics. The returned view points into  
     * memory owned by `*this`, and will be valid as long as `*this` or an object move-constructed  
     * from `*this` are alive.  
     *  
     * \par Complexity  
     * Constant.  
     */  
template <int I>  
void f();  
```  
  
Doxygen generates XML like this:  
  
```xml  
        <detaileddescription>  
<para><parameterlist kind="templateparam"><parameteritem>  
<parameternamelist>  
<parametername>I</parametername>  
</parameternamelist>  
<parameterdescription>  
<para>Resultset index. For operations returning more than one resultset, you can explicitly specify this parameter to obtain the number of affected rows by the i-th resultset. If left unspecified, the number of affected rows by the first resultset is returned.</para>  
</parameterdescription>  
</parameteritem>  
</parameterlist>  
<simplesect kind="par"><title>Preconditions</title><para><computeroutput>this-&gt;has_value() == true</computeroutput></para>  
</simplesect>  
<simplesect kind="par"><title>Exception safety</title><para>No-throw guarantee.</para>  
</simplesect>  
<simplesect kind="par"><title>Complexity</title><para>Constant. </para>  
</simplesect>  
</para>  
        </detaileddescription>  
```  
  
This is currently rendered by pydocca like this:  
  
![screenshot-docca](https://github.com/user-attachments/assets/643f8263-384f-47fb-bfe6-f7d417879584)
