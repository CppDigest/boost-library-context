# #159 - char reading in io_fields.hpp does not ignore newline and spaces [Open]

> Username: PedroAreiasIST  
> Created at: 2024-01-30 17:44:40 UTC  
> Updated at: 2024-01-30 17:44:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/159  

Since the opening curly braces '{' is read as a char, newlines and spaces set the state "failbit".  
  
This can be an issue for serializing containers in a struct, which usually require the size to be read and then the sequence.  
  
It would be silly to pollute users serialization functions for containers with the curly braces.  
  
Therefore, I changed my local copy of io_fields to read:  
 char parenthis = {}; // That is in the original.  
    do  
      {  
	in >> parenthis;  
      }  
    while(parenthis=='\n'||parenthis==' ');  
    if (parenthis != '{') in.setstate(std::basic_istream<Char, Traits>::failbit);  
  
Will think about a better solution later.
