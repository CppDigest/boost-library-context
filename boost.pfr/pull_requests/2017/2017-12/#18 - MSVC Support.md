# #18 MSVC Support [Closed]

> Username: Giperion  
> Created at: 2017-12-09 12:36:15 UTC  
> Updated at: 2018-01-10 20:14:01 UTC  
> Closed at: 2018-01-10 20:14:01 UTC  
> Url: https://github.com/boostorg/pfr/pull/18  

Вы бы написали что MSVC не поддерживается. А то я уж было обрадовался, что в кой веки шаблонная магия действительно приносит ощутимую пользу, но у меня это все не заработало.  
И судя по всему вы написали код для MSVC, но ни разу не протестировали его, иначе не было бы #error и #warning, которые я вообще впервые вижу.  
flat_tuple_size по сути закостылирован. Фиг его знает чё там, я в тонкостях метапрограммирования не разбираюсь, просто данный вариант лучше чем "Библиотека которая вообще не компилируется".  
  
Структура сериализуется, проверил.  
  
P.S. Данная штука - must-have для каждой сетевой или сериализующей либы, это уж точно. За проделанную работу (и статью на хабре) спасибо, но впредь не забываете про тестирование.

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-12-09 12:51:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/18#issuecomment-350460452  

[![Coverage Status](https://coveralls.io/builds/14579150/badge)](https://coveralls.io/builds/14579150)  
  
Coverage remained the same at 96.088% when pulling **3d8aa79603edc419eb42b87eb0862fc2849bf3af on Giperionn:develop** into **250d5011f4ffdbed4ca57a61291fd708fe155aaf on apolukhin:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2017-12-13 16:59:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/18#issuecomment-351454213  

MSVC поодерживается, покрыт авто тестами и CI, и можно убедиться что всё работает [вот тут](https://ci.appveyor.com/project/apolukhin/magic-get/branch/develop).  Нужна одна из последних версий.  
  
Использовать _MSVC_LANG  хорошая идея,  с радостью приму pull request который только добавляет его проверку.  
  
Патчи на поддержку более старых версий msvc так же приму с удовольствием.

---

## Comment 3

> Username: Giperion  
> Created_at: 2017-12-16 08:55:04 UTC  
> Updated_at: 2017-12-16 08:56:07 UTC  
> Url: https://github.com/boostorg/pfr/pull/18#issuecomment-352170653  

> MSVC поодерживается, покрыт авто тестами и CI, и можно убедиться что всё работает вот тут.   
  
Эмм...  
По вашей ссылке видно, как сборка свалилась. Все начинается на 1659 строчке. Appveyor как всегда тупит (Build Success, лол), впрочем ничего нового.  
Я использовал студию 15.3.0 когда первоначально попытался собрать вашу библиотеку.  
Сейчас я использую 15.5.1 (самая новая) с WinSDK: 10.0.15063 (не MinGW, полноценный SDK). Если откатить мой коммит для tuple_size.hpp то я все еще получаю ошибки:  
`1>f:\[eureka]\c++refl\include\boost\pfr\flat\tuple_size.hpp(36): error C2059: синтаксическая ошибка: size_v  
1>f:\[eureka]\c++refl\include\boost\pfr\flat\tuple_size.hpp(36): error C2993: unknown-type: недопустимый тип для параметра шаблона "Index", не являющегося типом`  
Не могу поверить, что мне приходится кого то еще уговаривать принять коммит с исправлениями >_\\\

---

## Comment 4

> Username: apolukhin  
> Created_at: 2017-12-20 08:02:14 UTC  
> Url: https://github.com/boostorg/pfr/pull/18#issuecomment-352992452  

Тест на строчке 1659 и [должен проваливаться](https://github.com/apolukhin/magic_get/blob/develop/test/Jamfile.v2#L52), тест благополучно проваливается и пишет об этом: `(failed-as-expected)`  
  
Приведите пожалуйста минимальный пример, на  котором библиотека неверно отрабатывает на MSVC.

---
