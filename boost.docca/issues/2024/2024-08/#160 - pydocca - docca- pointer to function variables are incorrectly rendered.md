# #160 - pydocca / docca: pointer to function variables are incorrectly rendered [Closed]

> Username: anarthal  
> Created at: 2024-08-14 21:08:36 UTC  
> Updated at: 2024-11-02 10:21:06 UTC  
> Closed at: 2024-11-02 10:21:06 UTC  
> Url: https://github.com/boostorg/docca/issues/160  

A member like:  
  
```  
struct character_set {  
    /**  
     * \brief Obtains the size of the first character of a string.  
     * \details  
     * Given a range of bytes, `r`, this function must interpret `r` as a  
     * string encoded using this character set, and return the number of  
     * bytes that the first character in the string spans, or 0 in case of error.  
     * `r` is guaranteed to be non-empty (`r.size() > 0`).  
     * \n  
     * In some character sets (like UTF-8), not all byte sequences represent  
     * valid characters. If this function finds an invalid byte sequence while  
     * trying to interpret the first character, it should return 0 to signal the error.  
     * \n  
     * This function must not throw exceptions or have side effects.  
     * \n  
     * \par Function signature  
     * The function signature should be:  
     * `std::size_t (*next_char)(boost::span<const unsigned char> r)`.  
     */  
    std::size_t (*next_char)(span<const unsigned char>);  
};  
```  
  
Is being rendered like [this](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__character_set/next_char.html) both in the XSLT-based and Python-based docca versions. I suspect the variable Jinja template is missing some parts to correctly handle such definitions. For reference, this is what the XML looks like for this member:  
  
```  
      <memberdef kind="variable" id="structboost_1_1mysql_1_1character__set_1a1980f1d3be94fc658ad050506a7e213f" prot="public" static="no" mutable="no">  
        <type>std::size_t(*</type>  
        <definition>std::size_t(* boost::mysql::character_set::next_char) (span&lt; const unsigned char &gt;)</definition>  
        <argsstring>)(span&lt; const unsigned char &gt;)</argsstring>  
        <name>next_char</name>  
        <qualifiedname>boost::mysql::character_set::next_char</qualifiedname>  
        <briefdescription>  
<para>Obtains the size of the first character of a string. </para>  
        </briefdescription>  
        <detaileddescription>  
<para>Given a range of bytes, <computeroutput>r</computeroutput>, this function must interpret <computeroutput>r</computeroutput> as a string encoded using this character set, and return the number of bytes that the first character in the string spans, or 0 in case of error. <computeroutput>r</computeroutput> is guaranteed to be non-empty (<computeroutput>r.size() &gt; 0</computeroutput>). <linebreak/>  
In some character sets (like UTF-8), not all byte sequences represent valid characters. If this function finds an invalid byte sequence while trying to interpret the first character, it should return 0 to signal the error. <linebreak/>  
This function must not throw exceptions or have side effects. <linebreak/>  
<simplesect kind="par"><title>Function signature</title><para>The function signature should be: <computeroutput>std::size_t (*next_char)(boost::span&lt;const unsigned char&gt; r)</computeroutput>. </para>  
</simplesect>  
</para>  
        </detaileddescription>  
        <inbodydescription>  
        </inbodydescription>  
        <location file="boost/mysql/character_set.hpp" line="57" column="5" bodyfile="boost/mysql/character_set.hpp" bodystart="57" bodyend="-1"/>  
      </memberdef>  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-08-15 02:04:14 UTC  
> Url: https://github.com/boostorg/docca/issues/160#issuecomment-2290361937  

I already handle a very similar situation with arrays, so It shouldn't be too hard to handle. It's very unfortuante that Doxygen does this silliness.

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-08-15 02:28:01 UTC  
> Url: https://github.com/boostorg/docca/issues/160#issuecomment-2290384395  

So, there's a larger problem here. Doxygen doesn't give us parsed parameters, only raw-ish `argsstring`. So, while I _can_ output the string, I cannot make types linkable.  
  
There are recurrent problems with Doxygen's handling of complex types, and also with how it resolves them. Because of that, the actual proper solution is to just parse types in Docca. But, as far as I know, that would require a non-trivial amount of work. Which is why I postponed it.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-08-15 03:11:23 UTC  
> Url: https://github.com/boostorg/docca/issues/160#issuecomment-2290465879  

Please, check if #162 fixes the issue.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-09-02 06:24:27 UTC  
> Url: https://github.com/boostorg/docca/issues/160#issuecomment-2323908387  

It looks like it did. Thanks.
