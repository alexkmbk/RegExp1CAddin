[![Join the chat at https://gitter.im/RegEx1CAddin/Lobby](https://badges.gitter.im/RegEx1CAddin.svg)](https://gitter.im/RegEx1CAddin/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

# RegEx1CAddin

Внешняя Native API компонента для выполнения регулярных выражений на платформе 1С:Предприятие 8. Написана на C++. Используется движок PCRE2 версии 10.36 (до версии 13, использовался boost::regex v 1.69). Версия синтаксиса Perl Compatible Regular Expressions.

Текущая версия собрана для следующих платформ:
- Windows 32bit    
- Windows 64bit    
- Linux 32bit   
- Linux 64bit   
- MacOS 64bit   
- Android ARMv7-A   
- Android x86-64   
- Google Chrome (Linux, Windows)   

Тестировалось на платформе 8.3.12.1567 (Windows 7, Windows Server 2008 R2, Ubuntu 14 32-64bit, MacOS Sierra 10.12, Android 8)

Сборка осуществлялась с использованием следующих инструментов:

- Под Windows: Microsoft Visual Studio Community 2017

- Под Linux: GCC 6

- Под Mac OS: Clang 9

- Под Android: Android Studio NDK 19.2 

Использовалась статическая сборка, поэтому компонента не требует установки каких-либо дополнительных библиотек.

Компонента реализует следующие методы:

### Метод НайтиСовпадения \ Matches(<Текст для анализа>, [<Регулярное выражение>], [<ИерархическийОбходРезультатов>]).

Метод выполняет поиск в переданном тексте по переданному регулярному выражению.

Результатом выполнения метода будет массив результатов поиска. 

Здесь, **<ИерархическийОбходРезультатов>** - определяет то, как будет осуществлен обход результатов. По умолчанию = Ложь. Если **ИерархическийОбходРезультатов=Ложь**, тогда каждый элемент массива результатов поиска - найденная подгруппа поиска. Если подгрупп нет, то массив будет содержать один элемент - найденную строку. Если **ИерархическийОбходРезультатов=Истина**, тогда,  каждый элемент массива результатов поиска будет содержать только найденную строку, а значение подгруппы при их наличии, можно будет получить методом **ПолучитьПодгруппу**. 

Возвращаемое значение: Ничего не возвращает.

Для того, чтобы получить результаты выполнения метода (массив результатов), необходимо выполнить метод Следующий/Next(), и после этого, в свойстве ТекущееЗначение/CurrentValue будет доступно значение текущей подгруппы результата выполнения (текущий элемент массива результатов). Идея  похожа на обход результата запроса.

Пример:

```bsl
Рег.НайтиСовпадения("Hello world", "([A-Za-z]+)\s+([a-z]+)");  

Пока Рег.Следующий() Цикл  
    Сообщить(Рег.ТекущееЗначение);       
КонецЦикла;   
```

Результат будет содержать 3 элемента:  

```
Hello world  
Hello  
world  
```

Пример с иерархическим обходом:  

```bsl
Рег.НайтиСовпадения("Hello world", "([A-Za-z]+)\s+([a-z]+)", Истина);  

Пока Рег.Следующий() Цикл  
    Сообщить(Рег.ТекущееЗначение); // Hello world  
    Сообщить(Рег.ПолучитьПодгруппу(0)); // Hello  
    Сообщить(Рег.ПолучитьПодгруппу(1)); // world  
КонецЦикла;  
```

Результат будет содержать 1 элемент и 2 подгруппы, а вывод будте таким же:  

```
Hello world  
Hello  
world  
```

### Метод Количество() \ Count()

Возвращает количество результатов поиска, после выполнения метода НайтиСовпадения / Matches

### Метод КоличествоВложенныхГрупп() \ SubMatchesCount()

Метод возвращает количество групп (подгрупп\SubMatches) если в шаблоне были заданы группы, например, для шаблона ([A-Za-z]+)\s+([a-z]+) будет возвращено значение 2. Метод возвращает значение только после выполнения метода НайтиСовпадения \ Matches.

### Метод ПолучитьПодгруппу \ GetSubMatch(<ИндексПодгруппы>)

Метод возвращает строковое значение подгруппы из результатов поиска методом НайтиСовпадения \ Matches. У метода один параметр - Индекс группы, он задает индекс группы, который следует получить(доступны значения от 0 до КоличествоВложенныхГрупп - 1).

### Метод НайтиСовпаденияJSON / MatchesJSON(<Текст для анализа>, [<Регулярное выражение>])

Метод выполняет поиск в переданном тексте по переданному регулярному выражению.

Результатом выполнения метода будет строка в формате JSON представляющая собой массив структур.

Метод позволяет значительно быстрее получить и обработать результат поиска (за счет минимизации вызовов методов через NativeAPI).

### Метод Заменить \ Replace(<Текст для анализа>, [<Регулярное выражение>], <Значение для замены>)

Заменяет в переданном тексте часть, соответствующую регулярному выражению, значением, переданным третьим параметром.

Возвращаемое значение: Строка, результат замены.

### Метод Совпадает \ IsMatch \ Test(<Текст для анализа>, [<Регулярное выражение>])

Делает проверку на соответствие текста регулярному выражению.

Возвращаемое значение: Булево. Возвращает значение Истина если текст соответствует регулярному выражению.

### Метод Версия \ Version()

Возвращает номер версии компоненты в виде строки.

Возвращаемое значение: Строка

### Свойство ВсеСовпадения \ Global

Тип: Булево.

Значение по умолчанию: Ложь.

Если установлено в Истина, то поиск будет выполняться по всем совпадениям, а не только по первому.

### Свойство Multiline \ Многострочный

Тип: Булево.

Значение по умолчанию: Истина.

Если установлено в Истина, то началом строки считаются также и символы перевода строки.

### Свойство ИгнорироватьРегистр \ IgnoreCase

Тип: Булево.

Значение по умолчанию: Ложь.

Если установлено в Истина, то поиск будет осуществляться без учета регистра.

### Свойство Шаблон \ Pattern

Тип: Строка.

Значение по умолчанию: пустая строка.

Задает регулярное выражение которое будет использоваться при вызове методов компоненты, если в метод не передано значение регулярного выражения.

### Свойство FirstIndex

Тип: Число.

Аналог свойства FirstIndex из VBScript.RegExp. Возвращает индекс (начинается с 0) первого символа найденного текста в исходной строке.

### Свойство ОписаниеОшибки \ ErrorDescription

Тип: Строка.

Значение по умолчанию: пустая строка.

Содержит текст последней ошибки. Если ошибки не было, то пустая строка.

### Свойство ВызыватьИсключения \ ThrowExceptions

Тип: Булево.

Значение по умолчанию: Ложь.

Если установлена в Истина, то при возникновении ошибки, будет вызываться исключение, при обработке исключения, текст ошибки можно получить из свойства ErrorDescription\ОписаниеОшибки.

### Пример использования:

Предполагается что архив с компонентами был загружен в общий макет "RegEx"

```bsl
УстановитьВнешнююКомпоненту("ОбщийМакет.RegEx");  
ПодключитьВнешнююКомпоненту("ОбщийМакет.RegEx", "Component", ТипВнешнейКомпоненты.Native);  
            
Рег = Новый("AddIn.Component.RegEx");  
Рег.НайтиСовпадения("Hello world", "([A-Za-z]+)\s+([a-z]+)", Истина);  

Сообщить(Рег.Количество()); // 1 - всего один результат   
Сообщить(Рег.КоличествоВложенныхГрупп()); // 2 - две подгруппы (submatches)  

Пока Рег.Следующий() Цикл  
    Сообщить(Рег.ТекущееЗначение); // Hello world     
    Сообщить(Рег.ПолучитьПодгруппу(0)); // Hello  
    Сообщить(Рег.ПолучитьПодгруппу(1)); // world  
КонецЦикла;  

Сообщить(Рег.Количество());  
Сообщить(Рег.Совпадает("Hello world", "([A-Za-z]+)\s+([a-z]+)"));  
Сообщить(Рег.Заменить("Hello world", "([A-Za-z]+)\s+([a-z]+)", "Текст для замены"));  
```

Примечание.
При использовании в шаблоне символов [\W] и [\w], особенностью реализации является то, что в качестве символов распознаются не только символы латиницы, а вообще любые символы, поэтому, если требуется отделить именно символы латиницы, рекомендуется использовать шаблоны [^a-zA-Z0-9_] и [a-zA-Z0-9_] соответственно.

Связанный проект - [Подсистема "Кроссплатформенные регулярные выражения в 1С"](https://github.com/cpr1c/RegEx1C_cfe/)
