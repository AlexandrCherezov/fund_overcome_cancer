# Обработка данных о подопечных фонда.
[Ссылка на файл](https://docs.google.com/spreadsheets/d/1bvDVmlPaN0ki0ik4_cy0qMnjUVtQjaaKYnDAWLnI2dc/edit?usp=sharing)

# После знакомства с данными принято решение об обработке средствами Google Sheets. Причины:
- Данные немногочисленные – 1569 записей
- На рынке существует востребованность навыков обработки данных в Excel и Google Sheets
- Ввиду множества вариантов представленных данных в таблицах их удобно и быстро корректировать 

# Ход обработки.
- Закрепили верхнюю область. Добавили нумерацию. По порядку установили фильтр
- Проверили столбцы на дубликаты. В столбцах ФИО и Телефон дубликаты не выявлены. В Столбце email существуют незаполненные значения, число которых рано числу дубликатов
- ОБРАБОТКА СТОЛБЦА ДАТА РОЖДЕНИЯ
  - Данные в столбце Дата рождения и sent привел к числовому формату
  - Рассчитал количество полных лет между датой отправки и датой рождения =РАЗНДАТ(E2; EG2; "Y")
  - Категоризировал данные и записал их в столбец Категория возраста
  - Удалил некорректные данные о дате рождения для подопечного User_01_1443
  - Сгруппировал служебные ячейки, оставил только результат - Категория возраста
- ОБРАБОТКА СТОЛБЦА ГОРОД
  - Удалили из столбца «Город» значения «с.», «г.» т другие аналогичные
  - Создал 3 пустых столбца и поместили в него функцию  =SPLIT(F27;" ")
  - Применил функцию =ДЛСТР(G1166) чтобы проверить короткие названия на корректность
  - В новом столбце «Регион» применили функцию 
     = ЕСЛИ(G28 = "Москва"; G28; ЕСЛИ(G28 = "Санкт-Петербург"; G28; ЕСЛИ(G28 = "Севастополь"; G28; СЦЕПИТЬ(H28;" ";I28;" ";J28))))
  - Оформил группировку по столбцам, чтобы оставить только обработанные данные
  - Визуально проверил и отредактировал столбец Регион
- ОБРАБОТКА СТОЛБЦА СФЕРА ДЕЯТЕЛЬНОСТИ
  - Применил сложную вложенную функцию для категоризации сферы деятельности пациента 
=ЕСЛИ(ЕОШИБКА(НАЙТИ("мед";$L8)>0);( ЕСЛИ(ЕОШИБКА(НАЙТИ("фарм";$L8)>0); 0;1));1)
  - Для определения собственно категории использовал функцию =ПРОСМОТРX(1;M2:AU2;$M$1:$AU$1;"Совпадений нет")
332 записи из 1569 (то есть пятая часть) остались в категории Другие . Из них не указаны сфера деятельности – у 194 записей. Таким образом, не удалось установить сферу занятости у 10% от указавших ее.
- ОБРАБОТКА СТОЛБЦА ДИАГНОЗ
  -  Применил сложную вложенную функцию для категоризации сферы деятельности пациента =ЕСЛИ(ЕОШИБКА(НАЙТИ())
  - Желтым фоном выделены колонки с исполняемым кодом
  - 86 записи из 1569 (то есть около 5%) остались в категории Совпадений нет
- ОБРАБОТКА СТОЛБЦА ЭТАП
  - Зеленым фоном выделены колонки с обработанным результатом
  - Примем следующие этапы лечения:
    - Постановка диагноза
    - Лечение
    - Реабилитация
    - Ремиссия
    - Рецидив
  - Заполним цифрой 1 ячейки в столбце «Постановка диагноза», если не заполнены другие ячейки
  - Используем =ЕСЛИ(ЕПУСТО(CN20); 1; 0), чтобы заполнить столбец «Не указан (этап)»
  - 27 записей из 1569 (то есть около 2%) остались в категории «НЕ указано» по причине отсутствия сведений
  - Примем следующие виды лечения:
    - Хирургическое лечение
    - Химиотерапия
    - Лучевая терапия
    - Гормональная терапия
    - Иммунотерапия
    - Таргетная терапия
  - Заполнили категории вида лечения
- ОБРАБОТКА СТОЛБЦА ПОМОЩЬ
  - 789 записей из 1569 (то есть около половины) остались в категории «НЕ указано» по причине отсутствия сведений
- ОБРАБОТКА СТОЛБЦА КАК УЗНАЛИ
  - 47 записей из 1569 (то есть около 3%) остались в категории «НЕ указано» по причине отсутствия сведений. Еще 56 записей (то есть около 4%) отнесены в категорию «Другие»
- ОБРАБОТКА СТОЛБЦА КТО ЗАПОЛНИЛ АНКЕТУ
- ОБРАБОТКА СТОЛБЦА ДАТА ЗАПОЛНЕНИЯ АНКЕТЫ
  - Столбец «Дата заполнения анкеты» имеет пропуски, а также значения до 2021 года. Учитывая, что дата отправки анкеты (столбец «sent») обычно совпадает, либо на день отличается от Столбца «Дата заполнения анкеты» для анализа используем именно столбец «sent»
  - Данные в столбцах requestid, sent, referrer, Form name не несет нам никакой информации. Удалим эти столбцы, а также столбец Дата заполнения анкеты
  - Данные в столбцах ФИО, Телефон, email не содержат дубликатов и они лишены возможности идентификации и получения каких- либо иных сведений – как то: пол пациента, привязки номера телефона к региону, выявления мобильного оператора, выявления хоста электронной почты. 
- ФОРМИРОВАНИЕ СТОЛБЦА СТАДИЯ ЗАБОЛЕВАНИЯ
  - Применим регулярные выражения, например, такое:
=ЕСЛИ(REGEXMATCH($AW2;"\d\D"); REGEXEXTRACT($AW2;"\d\D"); "не указана")
  - Из столбца «Диагноз» получим при возможности степень заболевания.
    - Если цифры арабские – получим цифру
    - Если степень зашифрована в коде Системы стадирования TNM – получим цифру после символа Т, записанного как латиницей, так и кириллицей
    - Если степень зашифрована в виде римской цифры, используем код =ЕСЛИ(REGEXMATCH($AW2;"[IV]+"); REGEXEXTRACT($AW2;"[IV]+"); "не указана")
  - После категоризации вручную поменяем 10 категоризированных значений на арабские цифры
  - После чего результат скопируем и вставим в виде неизменных значений, после чего применим регулярные выражения
  - Применим функцию совместно с регулярным выражением
=ARRAYFORMULA(ЕСЛИ(REGEXMATCH(EN2:ES2;"\d"); REGEXEXTRACT(EN2:ES2;"\d"); 0))
  - И далее – логическое выражение для определения стадии, выраженной цифрой:
=ЕСЛИ(EU2>0; EU2; ЕСЛИ(EV2>0; EV2; ЕСЛИ(EW2>0; EW2; ЕСЛИ(EX2>0; EX2; ЕСЛИ(EY2>0; EY2; ЕСЛИ(EZ2>0; EZ2; "не_указана"))))))
  - Если стадия заболевания 0, а также 5 и выше – приведем вручную к значениям «не указана»
- На голубом фоне – перенос значений (без формул)
- ОБРАБОТКА СТОЛБЦА SENT
  - Выделим год заполнения анкеты и месяц заполнения анкеты
- Таблица готова для анализа и построения дашборда
- Создал новый лист, на который перенес только столбцы с обработанными данными – чтобы не загружать датасет при импорте в DataLens. Столбцы копируют значения с рабочего листа.
 
# [Расположение файла](https://docs.google.com/spreadsheets/d/1bvDVmlPaN0ki0ik4_cy0qMnjUVtQjaaKYnDAWLnI2dc/edit?usp=sharing) 




