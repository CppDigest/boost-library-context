# #56 - write_xml and write_json may silently fail and corrupt data [Closed]

> Username: Reisse  
> Created at: 2020-07-17 09:45:22 UTC  
> Updated at: 2021-05-29 17:38:35 UTC  
> Closed at: 2021-05-29 17:38:35 UTC  
> Url: https://github.com/boostorg/property_tree/issues/56  

Both `write_xml(...)` and `write_json(...)` overloads accepting `const std::string &filename` look the following way:  
```  
    {  
        std::basic_ofstream<typename Ptree::key_type::value_type>  
            stream(filename.c_str());  
...  
        write_{xml, json}_internal(stream, pt, filename, settings);  
    }  
```  
where `write_{xml, json}_internal(...)` writes data to the stream and in the end checks that no errors happened:  
```  
        if (!stream)  
            BOOST_PROPERTY_TREE_THROW(xml_parser_error("write error", filename, 0));  
```  
The problem is that the actual write on disk (or at least part of it, depending on property_tree size) will be performed in `stream`'s dtor, when internal buffers will be flushed. Any error possibly happening there is silently ignored.  
  
Minimal reproducing example is this:  
```  
    pt::ptree tree;  
    tree.put("foo", "bar");  
    tree.put("bar", "baz");  
    try {  
        errno = 0;  
        pt::write_xml("dev_dir/ptee.xml", tree, std::locale(),  
                pt::xml_parser::xml_writer_settings<pt::ptree::key_type>(' ', 2));  
    } catch (pt::ptree_error & e) {  
        const int err = errno;  
        std::cerr << "write failed with " << e.what() << ", errno: " << err << std::endl;  
        return 2;  
    }  
    std::cout << "success" << std::endl;  
```  
If you execute this code on a disk partition where there is no space left, you'll see "success" in the stdout, but on disk there'll be empty file without any data.  
  
Explicitly doing `stream.flush()` and checking for errors afterwards should help.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-29 17:38:35 UTC  
> Url: https://github.com/boostorg/property_tree/issues/56#issuecomment-850870634  

merged. Thank you
