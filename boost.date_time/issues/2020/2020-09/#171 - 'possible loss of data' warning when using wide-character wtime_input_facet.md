# #171 - 'possible loss of data' warning when using wide-character wtime_input_facet [Open]

> Username: trueqbit  
> Created at: 2020-09-07 18:49:11 UTC  
> Updated at: 2022-03-16 10:01:00 UTC  
> Url: https://github.com/boostorg/date_time/issues/171  

Compilers emit a 'possible loss of data' warning when using `wtime_input_facet::get()`.  
E.g. Visual C++ emits "_warning C4244: 'argument': conversion from 'const wchar_t' to 'const _Elem', possible loss of data_" with the following code:   
  
    wstring s;  
    // in.imbue({ locale::classic(), new wtime_input_facet{L"%m-%e-%Y %H:%M:%S"} });  
    wistringstream in{ s };  
    ptime pt;  
    in >> pt;  
  
This is because `wtime_input_facet::get()` aka `time_input_facet<ptime,wchar_t,istreambuf_iterator<wchar_t>>::get()` checks for errors in regard to special formatting characters, and the error handling code path effectively converts the wide-character input sequence to a narrow-character std::string, `date_time::convert_string_type<wchar_t, char>` being the culprit in date_time library land.  
  
The call and warning message stack look like this:  
  
    >   26: date_time::convert_string_type<wchar_t,char>(const wstring& inp_str)  
    |-> 1260: date_time::wtime_input_face::check_special_value<ptime>(istreambuf_iterator<wchar_t>& sitr, istreambuf_iterator<wchar_t>& stream_end, ptime& tt, wchar_t c)  
      |-> 1125: date_time::wtime_input_face::get(istreambuf_iterator<wchar_t>& sitr, istreambuf_iterator<wchar_t>& stream_end, ios_base& ios_arg, ptime& t, wstring& tz_str, bool time_is_local)  
        |->  936: date_time::wtime_input_face::get(istreambuf_iterator<wchar_t>& sitr, istreambuf_iterator<wchar_t>& stream_end, ios_base& ios_arg, ptime& t)  
          |->   80: posix_time::operator>><wchar_t>(wistream& is, ptime& pt)  
  
  
I am currently uncertain how to fix this properly, but it is definitely a PITA because of the lengthy warning message stack.

---

## Comment 1

> Username: hajokirchhoff  
> Created at: 2022-03-16 10:00:23 UTC  
> Updated at: 2022-03-16 10:01:00 UTC  
> Url: https://github.com/boostorg/date_time/issues/171#issuecomment-1068940919  

At the minimum, the `convert_string_type` should use `narrow` or `widen` to properly convert `char` to `wchar_t` and vice versa, instead of passing a wchar_t* iterator to a string. This issue is even more bothersome, because the only place I find where it is used, is to throw an exception `std::ios_base::failure`, when the parsing of special chars failed. All it does there is to include the problematic string.  
  
IMHO, convert_to_string could/should be completely removed, since the conversion will fail for most non-english languages anyway and throwing `std::ios_base::failure` with the text "parse failed" should be enough info.  
  
And the warning is more than just "annoying" to us as we have a strict "warnings as errors" policy.
