# #169 - pydocca: conditional noexcept specifications [Closed]

> Username: anarthal  
> Created at: 2024-09-02 06:51:44 UTC  
> Updated at: 2024-10-03 15:46:34 UTC  
> Closed at: 2024-10-03 15:46:33 UTC  
> Url: https://github.com/boostorg/docca/issues/169  

Conditional noexcept specifications are not rendered correctly - they're always rendered as plain noexcept.  
  
This may require a level of heuristics to solve as, once again, Doxygen offers misleading information about the topic. For instance, this is the representation of `basic_format_context::basic_format_context`, which is `noexcept(std::is_nothrow_default_constructible<OutputString>::value)`:  
  
```xml  
      <memberdef kind="function" id="classboost_1_1mysql_1_1basic__format__context_1aa0fbc6a27a0b13dea8de517702df4b5a" prot="public" static="no" const="no" explicit="yes" inline="yes" noexcept="yes" virt="non-virtual">  
        <type></type>  
        <definition>boost::mysql::basic_format_context&lt; OutputString &gt;::basic_format_context</definition>  
        <argsstring>(format_options opts) noexcept(std::is_nothrow_default_constructible&lt; OutputString &gt;::value)</argsstring>  
        <name>basic_format_context</name>  
        <qualifiedname>boost::mysql::basic_format_context::basic_format_context</qualifiedname>  
        <param>  
          <type><ref refid="structboost_1_1mysql_1_1format__options" kindref="compound">format_options</ref></type>  
          <declname>opts</declname>  
        </param>  
        <briefdescription>  
<para>Constructor. </para>  
        </briefdescription>  
        <detaileddescription>  
<para>Uses a default-constructed <computeroutput>OutputString</computeroutput> as output string, and an empty error code as error state. This constructor can only be called if <computeroutput>OutputString</computeroutput> is default-constructible.</para>  
<para><simplesect kind="par"><title>Exception safety</title><para>Strong guarantee: exceptions thrown by default-constructing <computeroutput>OutputString</computeroutput> are propagated. </para>  
</simplesect>  
</para>  
        </detaileddescription>  
        <inbodydescription>  
        </inbodydescription>  
        <location file="boost/mysql/format_sql.hpp" line="343" column="14" bodyfile="boost/mysql/format_sql.hpp" bodystart="343" bodyend="347"/>  
      </memberdef>  
```  
  
The rendered section looks weird, because it states `noexcept` and then has a section on how possible exceptions are propagated.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-09-02 07:39:29 UTC  
> Url: https://github.com/boostorg/docca/issues/169#issuecomment-2324030162  

Yeah, I can add logic that pulls out conditional noexcept from argstring.

---

## Comment 2

> Username: anarthal  
> Created at: 2024-09-02 07:44:48 UTC  
> Url: https://github.com/boostorg/docca/issues/169#issuecomment-2324040353  

Hang on, I've just seen that Doxygen has made improvements in the latest version. Once I manage to fix all things that the new version broke, I'll let you know whether the upgrade fixed the noexcept issue.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-09-02 09:10:33 UTC  
> Updated at: 2024-09-02 09:11:40 UTC  
> Url: https://github.com/boostorg/docca/issues/169#issuecomment-2324222552  

Doxygen 1.12 includes a `noexceptexpression` attribute, so no parsing is required. However, using this probably requires addressing #170 first.  
  
```xml  
<memberdef kind="function" id="classboost_1_1mysql_1_1basic__format__context_1aa0fbc6a27a0b13dea8de517702df4b5a" prot="public" static="no" const="no" explicit="yes" inline="yes" noexcept="yes" noexceptexpression="std::is_nothrow_default_constructible&lt; OutputString &gt;::value" virt="non-virtual">  
        <type></type>  
        <definition>boost::mysql::basic_format_context&lt; OutputString &gt;::basic_format_context</definition>  
        <argsstring>(format_options opts)</argsstring>  
        <name>basic_format_context</name>  
        <qualifiedname>boost::mysql::basic_format_context::basic_format_context</qualifiedname>  
        <param>  
          <type><ref refid="structboost_1_1mysql_1_1format__options" kindref="compound">format_options</ref></type>  
          <declname>opts</declname>  
        </param>  
        <briefdescription>  
<para>Constructor. </para>  
        </briefdescription>  
        <detaileddescription>  
<para>Uses a default-constructed <computeroutput>OutputString</computeroutput> as output string, and an empty error code as error state. This constructor can only be called if <computeroutput>OutputString</computeroutput> is default-constructible.</para>  
<para><simplesect kind="par"><title>Exception safety</title><para>Strong guarantee: exceptions thrown by default-constructing <computeroutput>OutputString</computeroutput> are propagated. </para>  
</simplesect>  
</para>  
        </detaileddescription>  
        <inbodydescription>  
        </inbodydescription>  
        <location file="boost/mysql/format_sql.hpp" line="343" column="14" bodyfile="boost/mysql/format_sql.hpp" bodystart="343" bodyend="347"/>  
      </memberdef>  
```
