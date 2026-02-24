# #118 - List of non BOOST-prefixed macros [Open]

> Username: ned14  
> Created at: 2019-12-16 13:34:32 UTC  
> Updated at: 2020-03-04 17:00:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/118  

The following macros are missing a `BOOST_` prefix, which is against Boost library guidelines:  
  
```  
./boost/date_time/wrapping_int.hpp:#ifndef _DATE_TIME_WRAPPING_INT_HPP__  
./boost/date_time/wrapping_int.hpp:#define _DATE_TIME_WRAPPING_INT_HPP__  
./boost/date_time/int_adapter.hpp:#ifndef _DATE_TIME_INT_ADAPTER_HPP__  
./boost/date_time/int_adapter.hpp:#define _DATE_TIME_INT_ADAPTER_HPP__  
./boost/date_time/time_facet.hpp:#ifndef _DATE_TIME_FACET__HPP__  
./boost/date_time/time_facet.hpp:#define _DATE_TIME_FACET__HPP__  
./boost/date_time/date_parsing.hpp:#ifndef _DATE_TIME_DATE_PARSING_HPP___  
./boost/date_time/date_parsing.hpp:#define _DATE_TIME_DATE_PARSING_HPP___  
./boost/date_time/time_zone_base.hpp:#ifndef _DATE_TIME_TIME_ZONE_BASE__  
./boost/date_time/time_zone_base.hpp:#define _DATE_TIME_TIME_ZONE_BASE__  
./boost/date_time/adjust_functors.hpp:#ifndef _DATE_TIME_ADJUST_FUNCTORS_HPP___  
./boost/date_time/adjust_functors.hpp:#define _DATE_TIME_ADJUST_FUNCTORS_HPP___  
./boost/date_time/time_parsing.hpp:#ifndef _DATE_TIME_TIME_PARSING_HPP___  
./boost/date_time/time_parsing.hpp:#define _DATE_TIME_TIME_PARSING_HPP___  
./boost/date_time/local_time/posix_time_zone.hpp:#ifndef _DATE_TIME_POSIX_TIME_ZONE__  
./boost/date_time/local_time/posix_time_zone.hpp:#define _DATE_TIME_POSIX_TIME_ZONE__  
./boost/date_time/local_time/posix_time_zone.hpp:#endif // _DATE_TIME_POSIX_TIME_ZONE__  
./boost/date_time/date_facet.hpp:#ifndef _DATE_TIME_DATE_FACET__HPP___  
./boost/date_time/date_facet.hpp:#define _DATE_TIME_DATE_FACET__HPP___  
./boost/date_time/date_generator_formatter.hpp:#ifndef _DATE_TIME_DATE_GENERATOR_FORMATTER__HPP___  
./boost/date_time/date_generator_formatter.hpp:#define _DATE_TIME_DATE_GENERATOR_FORMATTER__HPP___  
./boost/date_time/date_generator_formatter.hpp:#endif // _DATE_TIME_DATE_GENERATOR_FORMATTER__HPP___  
./libs/date_time/src/gregorian/greg_names.hpp:#ifndef DATE_TIME_SRC_GREG_NAMES_HPP___  
./libs/date_time/src/gregorian/greg_names.hpp:#define DATE_TIME_SRC_GREG_NAMES_HPP___  
./libs/date_time/src/gregorian/greg_names.hpp:#endif // DATE_TIME_SRC_GREG_NAMES_HPP___  
./boost/date_time/tz_db_base.hpp:#ifndef DATE_TIME_TZ_DB_BASE_HPP__  
./boost/date_time/tz_db_base.hpp:#define DATE_TIME_TZ_DB_BASE_HPP__  
./boost/date_time/tz_db_base.hpp:#endif // DATE_TIME_TZ_DB_BASE_HPP__  
./boost/date_time/time_formatting_streams.hpp:#ifndef DATE_TIME_TIME_FORMATTING_STREAMS_HPP___  
./boost/date_time/time_formatting_streams.hpp:#define DATE_TIME_TIME_FORMATTING_STREAMS_HPP___  
./boost/date_time/dst_transition_generators.hpp:#ifndef DATE_TIME_DATE_DST_TRANSITION_DAY_GEN_HPP__  
./boost/date_time/dst_transition_generators.hpp:#define DATE_TIME_DATE_DST_TRANSITION_DAY_GEN_HPP__  
./boost/date_time/time_defs.hpp:#ifndef DATE_TIME_TIME_PRECISION_LIMITS_HPP  
./boost/date_time/time_defs.hpp:#define DATE_TIME_TIME_PRECISION_LIMITS_HPP  
./boost/date_time/date.hpp:#ifndef DATE_TIME_DATE_HPP___  
./boost/date_time/date.hpp:#define DATE_TIME_DATE_HPP___  
./boost/date_time/format_date_parser.hpp:#ifndef DATE_TIME_FORMAT_DATE_PARSER_HPP__  
./boost/date_time/format_date_parser.hpp:#define DATE_TIME_FORMAT_DATE_PARSER_HPP__  
./boost/date_time/time_duration.hpp:#ifndef DATE_TIME_TIME_DURATION_HPP___  
./boost/date_time/time_duration.hpp:#define DATE_TIME_TIME_DURATION_HPP___  
./boost/date_time/time_resolution_traits.hpp:#ifndef DATE_TIME_TIME_RESOLUTION_TRAITS_HPP  
./boost/date_time/time_resolution_traits.hpp:#define DATE_TIME_TIME_RESOLUTION_TRAITS_HPP  
./boost/date_time/special_defs.hpp:#ifndef DATE_TIME_SPECIAL_DEFS_HPP__  
./boost/date_time/special_defs.hpp:#define DATE_TIME_SPECIAL_DEFS_HPP__  
./boost/date_time/c_time.hpp:#ifndef DATE_TIME_C_TIME_HPP___  
./boost/date_time/c_time.hpp:#define DATE_TIME_C_TIME_HPP___  
./boost/date_time/c_time.hpp:#endif // DATE_TIME_C_TIME_HPP___  
./boost/date_time/parse_format_base.hpp:#ifndef DATE_TIME_PARSE_FORMAT_BASE__  
./boost/date_time/parse_format_base.hpp:#define DATE_TIME_PARSE_FORMAT_BASE__  
./boost/date_time/local_time_adjustor.hpp:#ifndef DATE_TIME_LOCAL_TIME_ADJUSTOR_HPP__  
./boost/date_time/local_time_adjustor.hpp:#define DATE_TIME_LOCAL_TIME_ADJUSTOR_HPP__  
./boost/date_time/date_formatting_limited.hpp:#ifndef DATE_TIME_DATE_FORMATTING_LIMITED_HPP___  
./boost/date_time/date_formatting_limited.hpp:#define DATE_TIME_DATE_FORMATTING_LIMITED_HPP___  
./boost/date_time/time_zone_names.hpp:#ifndef DATE_TIME_TIME_ZONE_NAMES_HPP__  
./boost/date_time/time_zone_names.hpp:#define DATE_TIME_TIME_ZONE_NAMES_HPP__  
./boost/date_time/time_system_counted.hpp:#ifndef DATE_TIME_TIME_SYSTEM_COUNTED_HPP  
./boost/date_time/time_system_counted.hpp:#define DATE_TIME_TIME_SYSTEM_COUNTED_HPP  
./boost/date_time/date_formatting_locales.hpp:#ifndef DATE_TIME_DATE_FORMATTING_LOCALES_HPP___  
./boost/date_time/date_formatting_locales.hpp:#define DATE_TIME_DATE_FORMATTING_LOCALES_HPP___  
./boost/date_time/date_format_simple.hpp:#ifndef DATE_TIME_SIMPLE_FORMAT_HPP___  
./boost/date_time/date_format_simple.hpp:#define DATE_TIME_SIMPLE_FORMAT_HPP___  
./boost/date_time/posix_time/ptime.hpp:#endif // DATE_TIME_NO_DEFAULT_CONSTRUCTOR  
./boost/date_time/posix_time/posix_time_io.hpp:#ifndef DATE_TIME_POSIX_TIME_IO_HPP__  
./boost/date_time/posix_time/posix_time_io.hpp:#define DATE_TIME_POSIX_TIME_IO_HPP__  
./boost/date_time/posix_time/posix_time_io.hpp:#endif // DATE_TIME_POSIX_TIME_IO_HPP__  
./boost/date_time/date_defs.hpp:#ifndef DATE_TIME_DATE_DEFS_HPP  
./boost/date_time/date_defs.hpp:#define DATE_TIME_DATE_DEFS_HPP  
./boost/date_time/gregorian/greg_ymd.hpp:#ifndef DATE_TIME_GREG_YMD_HPP__  
./boost/date_time/gregorian/greg_ymd.hpp:#define DATE_TIME_GREG_YMD_HPP__  
./boost/date_time/gregorian/gregorian_io.hpp:#ifndef DATE_TIME_GREGORIAN_IO_HPP__  
./boost/date_time/gregorian/gregorian_io.hpp:#define DATE_TIME_GREGORIAN_IO_HPP__  
./boost/date_time/gregorian/gregorian_io.hpp:#endif // DATE_TIME_GREGORIAN_IO_HPP__  
./boost/date_time/microsec_time_clock.hpp:#ifndef DATE_TIME_HIGHRES_TIME_CLOCK_HPP___  
./boost/date_time/microsec_time_clock.hpp:#define DATE_TIME_HIGHRES_TIME_CLOCK_HPP___  
./boost/date_time/time_system_split.hpp:#ifndef DATE_TIME_TIME_SYSTEM_SPLIT_HPP  
./boost/date_time/time_system_split.hpp:#define DATE_TIME_TIME_SYSTEM_SPLIT_HPP  
./boost/date_time/period.hpp:#ifndef DATE_TIME_PERIOD_HPP___  
./boost/date_time/period.hpp:#define DATE_TIME_PERIOD_HPP___  
./boost/date_time/special_values_parser.hpp:#ifndef DATE_TIME_SPECIAL_VALUES_PARSER_HPP__  
./boost/date_time/special_values_parser.hpp:#define DATE_TIME_SPECIAL_VALUES_PARSER_HPP__  
./boost/date_time/special_values_parser.hpp:#endif // DATE_TIME_SPECIAL_VALUES_PARSER_HPP__  
./boost/date_time/strings_from_facet.hpp:#ifndef DATE_TIME_STRINGS_FROM_FACET__HPP___  
./boost/date_time/strings_from_facet.hpp:#define DATE_TIME_STRINGS_FROM_FACET__HPP___  
./boost/date_time/date_names_put.hpp:#ifndef DATE_TIME_DATE_NAMES_PUT_HPP___  
./boost/date_time/date_names_put.hpp:#define DATE_TIME_DATE_NAMES_PUT_HPP___  
./boost/date_time/gregorian_calendar.hpp:#ifndef DATE_TIME_GREGORIAN_CALENDAR_HPP__  
./boost/date_time/gregorian_calendar.hpp:#define DATE_TIME_GREGORIAN_CALENDAR_HPP__  
./boost/date_time/date_generator_parser.hpp:#ifndef DATE_TIME_DATE_GENERATOR_PARSER_HPP__  
./boost/date_time/date_generator_parser.hpp:#define DATE_TIME_DATE_GENERATOR_PARSER_HPP__  
./boost/date_time/date_generator_parser.hpp:#endif // DATE_TIME_DATE_GENERATOR_PARSER_HPP__  
./boost/date_time/local_time/conversion.hpp:#ifndef DATE_TIME_LOCAL_TIME_CONVERSION_HPP__  
./boost/date_time/local_time/conversion.hpp:#define DATE_TIME_LOCAL_TIME_CONVERSION_HPP__  
./boost/date_time/local_time/conversion.hpp:#endif // DATE_TIME_LOCAL_TIME_CONVERSION_HPP__  
./boost/date_time/date_duration.hpp:#ifndef DATE_TIME_DATE_DURATION__  
./boost/date_time/date_duration.hpp:#define DATE_TIME_DATE_DURATION__  
./boost/date_time/locale_config.hpp:#ifndef DATE_TIME_LOCALE_CONFIG_HPP___  
./boost/date_time/locale_config.hpp:#define DATE_TIME_LOCALE_CONFIG_HPP___  
./boost/date_time/local_timezone_defs.hpp:#ifndef DATE_TIME_LOCAL_TIMEZONE_DEFS_HPP__  
./boost/date_time/local_timezone_defs.hpp:#define DATE_TIME_LOCAL_TIMEZONE_DEFS_HPP__  
./boost/date_time/time_clock.hpp:#ifndef DATE_TIME_TIME_CLOCK_HPP___  
./boost/date_time/time_clock.hpp:#define DATE_TIME_TIME_CLOCK_HPP___  
./boost/date_time/date_formatting.hpp:#ifndef DATE_TIME_DATE_FORMATTING_HPP___  
./boost/date_time/date_formatting.hpp:#define DATE_TIME_DATE_FORMATTING_HPP___  
./boost/date_time/time.hpp:#ifndef DATE_TIME_TIME_HPP___  
./boost/date_time/time.hpp:#define DATE_TIME_TIME_HPP___  
./boost/date_time/c_local_time_adjustor.hpp:#ifndef DATE_TIME_C_LOCAL_TIME_ADJUSTOR_HPP__  
./boost/date_time/c_local_time_adjustor.hpp:#define DATE_TIME_C_LOCAL_TIME_ADJUSTOR_HPP__  
./boost/date_time/compiler_config.hpp:#ifndef DATE_TIME_COMPILER_CONFIG_HPP___  
./boost/date_time/compiler_config.hpp:#define DATE_TIME_COMPILER_CONFIG_HPP___  
./boost/date_time/compiler_config.hpp://#define DATE_TIME_NO_DEFAULT_CONSTRUCTOR  
./boost/date_time/date_generators.hpp:#ifndef DATE_TIME_DATE_GENERATORS_HPP__  
./boost/date_time/date_generators.hpp:#define DATE_TIME_DATE_GENERATORS_HPP__  
./boost/date_time/dst_rules.hpp:#ifndef DATE_TIME_DST_RULES_HPP__  
./boost/date_time/dst_rules.hpp:#define DATE_TIME_DST_RULES_HPP__  
./boost/date_time/time_iterator.hpp:#ifndef DATE_TIME_TIME_ITERATOR_HPP___  
./boost/date_time/time_iterator.hpp:#define DATE_TIME_TIME_ITERATOR_HPP___  
./boost/date_time/filetime_functions.hpp:#ifndef DATE_TIME_FILETIME_FUNCTIONS_HPP__  
./boost/date_time/filetime_functions.hpp:#define DATE_TIME_FILETIME_FUNCTIONS_HPP__  
./boost/date_time/filetime_functions.hpp:#endif // DATE_TIME_FILETIME_FUNCTIONS_HPP__  
./boost/date_time/period_formatter.hpp:#ifndef DATETIME_PERIOD_FORMATTER_HPP___  
./boost/date_time/period_formatter.hpp:#define DATETIME_PERIOD_FORMATTER_HPP___  
./boost/date_time/period_parser.hpp:#ifndef DATETIME_PERIOD_PARSER_HPP___  
./boost/date_time/period_parser.hpp:#define DATETIME_PERIOD_PARSER_HPP___  
./boost/date_time/period_parser.hpp:#endif // DATETIME_PERIOD_PARSER_HPP___  
./boost/date_time/special_values_formatter.hpp:#ifndef DATETIME_SPECIAL_VALUE_FORMATTER_HPP___  
./boost/date_time/special_values_formatter.hpp:#define DATETIME_SPECIAL_VALUE_FORMATTER_HPP___  
./boost/date_time/posix_time/time_serialize.hpp:#ifndef POSIX_TIME_SERIALIZE_HPP___  
./boost/date_time/posix_time/time_serialize.hpp:#define POSIX_TIME_SERIALIZE_HPP___  
./boost/date_time/posix_time/posix_time_config.hpp:#ifndef POSIX_TIME_CONFIG_HPP___  
./boost/date_time/posix_time/posix_time_config.hpp:#define POSIX_TIME_CONFIG_HPP___  
./boost/date_time/posix_time/conversion.hpp:#ifndef POSIX_TIME_CONVERSION_HPP___  
./boost/date_time/posix_time/conversion.hpp:#define POSIX_TIME_CONVERSION_HPP___  
./boost/date_time/posix_time/posix_time.hpp:#ifndef POSIX_TIME_HPP___  
./boost/date_time/posix_time/posix_time.hpp:#define POSIX_TIME_HPP___  
./boost/date_time/posix_time/posix_time_system.hpp:#ifndef POSIX_TIME_SYSTEM_HPP___  
./boost/date_time/posix_time/posix_time_system.hpp:#define POSIX_TIME_SYSTEM_HPP___  
./boost/date_time/posix_time/time_period.hpp:#ifndef POSIX_TIME_PERIOD_HPP___  
./boost/date_time/posix_time/time_period.hpp:#define POSIX_TIME_PERIOD_HPP___  
./boost/date_time/posix_time/ptime.hpp:#ifndef POSIX_PTIME_HPP___  
./boost/date_time/posix_time/ptime.hpp:#define POSIX_PTIME_HPP___  
./boost/date_time/posix_time/posix_time_duration.hpp:#ifndef POSIX_TIME_DURATION_HPP___  
./boost/date_time/posix_time/posix_time_duration.hpp:#define POSIX_TIME_DURATION_HPP___  
./boost/date_time/posix_time/posix_time_legacy_io.hpp:#ifndef POSIX_TIME_PRE133_OPERATORS_HPP___  
./boost/date_time/posix_time/posix_time_legacy_io.hpp:#define POSIX_TIME_PRE133_OPERATORS_HPP___  
./boost/date_time/posix_time/posix_time_legacy_io.hpp:#endif // POSIX_TIME_PRE133_OPERATORS_HPP___  
./boost/date_time/posix_time/posix_time_types.hpp:#ifndef POSIX_TIME_TYPES_HPP___  
./boost/date_time/posix_time/posix_time_types.hpp:#define POSIX_TIME_TYPES_HPP___  
./boost/date_time/date_duration_types.hpp:#ifndef DATE_DURATION_TYPES_HPP___  
./boost/date_time/date_duration_types.hpp:#define DATE_DURATION_TYPES_HPP___  
./boost/date_time/date_duration_types.hpp:#endif // DATE_DURATION_TYPES_HPP___  
./boost/date_time/posix_time/date_duration_operators.hpp:#ifndef DATE_DURATION_OPERATORS_HPP___  
./boost/date_time/posix_time/date_duration_operators.hpp:#define DATE_DURATION_OPERATORS_HPP___  
./boost/date_time/posix_time/date_duration_operators.hpp:#endif // DATE_DURATION_OPERATORS_HPP___  
./boost/date_time/local_time/custom_time_zone.hpp:#ifndef LOCAL_TIME_CUSTOM_TIME_ZONE_HPP__  
./boost/date_time/local_time/custom_time_zone.hpp:#define LOCAL_TIME_CUSTOM_TIME_ZONE_HPP__  
./boost/date_time/local_time/local_date_time.hpp:#ifndef LOCAL_TIME_LOCAL_DATE_TIME_HPP__  
./boost/date_time/local_time/local_date_time.hpp:#define LOCAL_TIME_LOCAL_DATE_TIME_HPP__  
./boost/date_time/local_time/date_duration_operators.hpp:#ifndef LOCAL_TIME_DATE_DURATION_OPERATORS_HPP___  
./boost/date_time/local_time/date_duration_operators.hpp:#define LOCAL_TIME_DATE_DURATION_OPERATORS_HPP___  
./boost/date_time/local_time/date_duration_operators.hpp:#endif // LOCAL_TIME_DATE_DURATION_OPERATORS_HPP___  
./boost/date_time/local_time/dst_transition_day_rules.hpp:#ifndef LOCAL_TIME_DST_TRANSITION_DAY_RULES_HPP__  
./boost/date_time/local_time/dst_transition_day_rules.hpp:#define LOCAL_TIME_DST_TRANSITION_DAY_RULES_HPP__  
./boost/date_time/local_time/local_time_types.hpp:#ifndef LOCAL_TIME_LOCAL_TIME_TYPES_HPP__  
./boost/date_time/local_time/local_time_types.hpp:#define LOCAL_TIME_LOCAL_TIME_TYPES_HPP__  
./boost/date_time/local_time/local_time_types.hpp:#endif // LOCAL_TIME_LOCAL_TIME_TYPES_HPP__  
./boost/date_time/local_time/local_time.hpp:#ifndef LOCAL_TIME_LOCAL_TIME_HPP__  
./boost/date_time/local_time/local_time.hpp:#define LOCAL_TIME_LOCAL_TIME_HPP__  
./boost/date_time/iso_format.hpp:#ifndef ISO_FORMAT_HPP___  
./boost/date_time/iso_format.hpp:#define ISO_FORMAT_HPP___  
./boost/date_time/gregorian/formatters.hpp:#ifndef GREGORIAN_FORMATTERS_HPP___  
./boost/date_time/gregorian/formatters.hpp:#define GREGORIAN_FORMATTERS_HPP___  
./boost/date_time/gregorian/parsers.hpp:#ifndef GREGORIAN_PARSERS_HPP___  
./boost/date_time/gregorian/parsers.hpp:#define GREGORIAN_PARSERS_HPP___  
./boost/date_time/gregorian/formatters_limited.hpp:#ifndef GREGORIAN_FORMATTERS_LIMITED_HPP___  
./boost/date_time/gregorian/formatters_limited.hpp:#define GREGORIAN_FORMATTERS_LIMITED_HPP___  
./boost/date_time/gregorian/greg_calendar.hpp:#ifndef GREGORIAN_GREGORIAN_CALENDAR_HPP__  
./boost/date_time/gregorian/greg_calendar.hpp:#define GREGORIAN_GREGORIAN_CALENDAR_HPP__  
./boost/date_time/gregorian/gregorian.hpp:#ifndef GREGORIAN_HPP__  
./boost/date_time/gregorian/gregorian.hpp:#define GREGORIAN_HPP__  
./boost/date_time/gregorian/greg_serialize.hpp:#ifndef GREGORIAN_SERIALIZE_HPP___  
./boost/date_time/gregorian/greg_serialize.hpp:#define GREGORIAN_SERIALIZE_HPP___  
./boost/date_time/gregorian/greg_facet.hpp:#ifndef GREGORIAN_FACET_HPP___  
./boost/date_time/gregorian/greg_facet.hpp:#define GREGORIAN_FACET_HPP___  
./boost/date_time/gregorian/greg_day.hpp:#ifndef GREG_DAY_HPP___  
./boost/date_time/gregorian/greg_day.hpp:#define GREG_DAY_HPP___  
./boost/date_time/gregorian/greg_duration.hpp:#ifndef GREG_DURATION_HPP___  
./boost/date_time/gregorian/greg_duration.hpp:#define GREG_DURATION_HPP___  
./boost/date_time/gregorian/greg_date.hpp:#ifndef GREG_DATE_HPP___  
./boost/date_time/gregorian/greg_date.hpp:#define GREG_DATE_HPP___  
./boost/date_time/gregorian/greg_month.hpp:#ifndef GREG_MONTH_HPP___  
./boost/date_time/gregorian/greg_month.hpp:#define GREG_MONTH_HPP___  
./boost/date_time/gregorian/greg_weekday.hpp:#ifndef GREG_WEEKDAY_HPP___  
./boost/date_time/gregorian/greg_weekday.hpp:#define GREG_WEEKDAY_HPP___  
./boost/date_time/gregorian/greg_year.hpp:#ifndef GREG_YEAR_HPP___  
./boost/date_time/gregorian/greg_year.hpp:#define GREG_YEAR_HPP___  
./boost/date_time/gregorian/greg_duration_types.hpp:#ifndef GREG_DURATION_TYPES_HPP___  
./boost/date_time/gregorian/greg_duration_types.hpp:#define GREG_DURATION_TYPES_HPP___  
./boost/date_time/gregorian/greg_duration_types.hpp:#endif // GREG_DURATION_TYPES_HPP___  
./boost/date_time/gregorian/greg_day_of_year.hpp:#ifndef GREG_DAY_OF_YEAR_HPP___  
./boost/date_time/gregorian/greg_day_of_year.hpp:#define GREG_DAY_OF_YEAR_HPP___  
./boost/date_time/date_clock_device.hpp:#ifndef DATE_CLOCK_DEVICE_HPP___  
./boost/date_time/date_clock_device.hpp:#define DATE_CLOCK_DEVICE_HPP___  
./boost/date_time/constrained_value.hpp:#ifndef CONSTRAINED_VALUE_HPP___  
./boost/date_time/constrained_value.hpp:#define CONSTRAINED_VALUE_HPP___  
```

---

## Comment 1

> Username: JeffGarland  
> Created at: 2019-12-16 14:52:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566094329  

It's quite possible the guidelines were written well after date-time was in boost (1.23).  And is there some actual reported issue from the field other than consistency?  
  
Of course pull requests are accepted, but I'm not planning to spend time fixing something that isn't really broken.

---

## Comment 2

> Username: ned14  
> Created at: 2019-12-16 14:59:21 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566097102  

I can supply a sed script which will fix the whole thing for you within a few seconds. Interested?

---

## Comment 3

> Username: JeffGarland  
> Created at: 2019-12-16 15:10:24 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566101666  

sure. run it and submit the pull request :)

---

## Comment 4

> Username: ned14  
> Created at: 2019-12-16 15:12:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566102613  

It's not as easy as that Jeff. I can report bugs in work time. I cannot supply PRs without going through Legal and getting permission for the patchset first. It is what it is.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2019-12-16 15:25:45 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566108379  

Ok, that's fair.  Send me the script and we'll see if I (or someone else) can work it in.

---

## Comment 6

> Username: ned14  
> Created at: 2019-12-16 15:28:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566109678  

Thanks for understanding. It is more than frustrating for me to have to tell people exactly how to replicate something which I could send in a PR in less time and effort.  
  
It's not me doing the work which yielded these issues, but I expect to be able to extract the appropriate sed script for this specific library after he's finished validation in a day or two's time. So, I'll be in touch.

---

## Comment 7

> Username: JeffGarland  
> Created at: 2019-12-16 15:31:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566110993  

Yep, this issue is one of the reasons I stopped being a direct employee, which I realize isn't for everyone.  So yep I'm familiar with the ugly corporate restrictions and the frustration it causes.

---

## Comment 8

> Username: ned14  
> Created at: 2019-12-16 15:33:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-566111879  

I only just left the life of a contractor earlier this year for the first time since 2013! As the single breadwinner with small children at home, the recent IT bubble instability worried me too much, so I rejoined the ranks of the salarymen.

---

## Comment 9

> Username: jeking3  
> Created at: 2020-03-04 17:00:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/118#issuecomment-594661679  

I'm not convinced that the preprocessor naming rules need necessarily apply to the include guards for legacy code, but it's not a difficult fix.
