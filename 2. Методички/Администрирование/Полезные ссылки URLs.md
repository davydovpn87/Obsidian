

##### Локальные сервера
- S3 https://s3-exp.teko-com.ru:9001/login
- Rabbit prod https://rmq.local.teko-com.ru:15671/#/queues
- Raqbbit test http://rmq-test01.local.teko-com.ru:15672/#/queues
- PBX https://web.pbxmaker.ru/#!/auth
- B24 https://bitrix-crm.teko-com.ru/auth/index.php?backurl=%2Fcrm%2Fdeal%2Fcategory%2F0%2F
* B24 Test https://test-bitrix-elma.teko-com.ru/crm/lead/details/360684/
* B24 Test https://bitrix-test.local.teko-com.ru/ home/bitrix/www/log/error
* 1C online https://as06.local.teko-com.ru/erp_main/#e1cib/data/


эцп
https://cryptopro.ru/sites/default/files/products/cades/demopage/cades_bes_sample.html
https://diadoc.kontur.ru/Box/Selection?back=%2F%3Fp%3Df00537%26utm_abtest%3Dactionkontur2_default%26utm_startpage%3Dkontur.ru%26utm_orderpage%3Dkontur.ru


##### Документация
- Имена компов http://puppet.local.teko-com.ru/list_ws.txt
- Доп. колонки к таблице в Елме https://wiki.local.teko-com.ru/it/ElmaDynamicGrid
- Сервисы https://wiki.local.teko-com.ru/it/%D0%A1%D0%B5%D1%80%D0%B2%D0%B8%D1%81%D1%8B_%D0%BD%D0%B0_.NET8#.D0.92.D0.B8.D1.80.D1.82.D1.83.D0.B0.D0.BB.D0.BA.D0.B0_elma-service01_.28.D0.BE.D0.BD.D0.B0_.D0.B6.D0.B5_elma-service.29

##### Внешние сервисы
* Контур https://auth.kontur.ru/?customize=diadoc&back=http%3A%2F%2Fdiadoc.kontur.ru%2Fdeb15a3c-9cb8-4be3-842f-8ed515a607cf%2FFolder%2FInbox
* Личный кабинет сбера
https://developers.sber.ru/studio/workspaces/14b851e0-50fc-4ee3-95fc-1c8518753ddb/gigachat-api/projects/57043b95-1027-426a-a128-b8d7e2d15232/parameters

##### Внешние документации
- GigaChat Работа с документами https://giga.chat/help/articles/work-with-docs
- GigaChat Загрузить файл https://developers.sber.ru/docs/ru/gigachat/api/reference/rest/post-file

##### Команда 
- http://ws0429 Паша
- http://ws0480 Саша
- http://ws0485 Дима
- http://ws0283 Сережа
- http://ws0395 Леня
- http://elma02 Оля

##### Дальний ящик
* ELMA 365 https://portal.elma365.ru/_login?returnUrl=%2F_blocked
* ELMA 365 https://elma365-etalon4.teko-com.ru/_frozen
* ELMA 365 https://elma365.teko-com.ru/
* ELMA 365 https://elma365-etalon2.teko-com.ru/
* ELMA 365 https://elma365-etalon3.teko-com.ru/
* ELMA 365 https://elma365-etalon4.teko-com.ru/	phone + бп
* ELMA 365 https://elma365-etalon5.teko-com.ru/	корреспонеденция




##### Телега
net 8 lunchSettings

Выпадающий список 500 элементов разор отчет
https://kb.elma-bpm.com/article-3585.html
Компания мечты
https://habr.com/ru/articles/865372/
Работа с JavaScript в ELMA 
https://kb.elma-bpm.com/KB/article-6688.html
Выбор счетов клиентов из таблицы
https://www.elma-bpm.ru/KB/article-6169.html
Добавление колонок таблицы в стандартный элемент "Dynamic Grid"
https://www.elma-bpm.ru/KB/article-6124.html
Telgram бот на C# через webhook
https://habr.com/ru/sandbox/188818/
❗️❗️❗️курсор среда программирования с ии

вызов cmd из елмы

или
git auto commit
git add -u
git commit -m "your commit message"
git push origin master 
#!/bin/bash
git commit -a -m "autoupdate date +%F-%T"
git push

sublime merge

Распознавание звонка Тинькоф
https://github.com/Tinkoff/voicekit-examples/tree/master/csharp
https://software.tbank.ru/docs/voicekit/recognize


{
    "type" : "Zakaz",
    "value" : {
        "NUMBER" : 489447,                                  --Номер Заказа в производство
        "DATE_DOC" : "26.12.2023 00:00:00",    -- Дата создания Заказа в производство
        "ROUTE_DOC_ID" : 489549,                      -- Документ из МЯ (Отпуск в производство или Отпуск в ремонт)
        "NOM_ID" : 117871,                                   -- код номенклатуры HiTek изделие
        "C1_REF" : "f304aa77-7448-11e3-b595-002522bf6f2f", -- 1С код номенклатуры
        "KOD_IZD" : "00-00026782",                    -- 1С код изделия
        "ZAKAZ_NUM" : "2312-0489447",          -- Номер партии 1С
        "NUM" : "00000024061",                         -- Номер заявки 1С
        "DATA_Z" : "26.12.2023 00:00:00",        -- Дата заказа 1С
        "GUID_ZAK" : "e83016b7-a3ec-11ee-8455-ac1f6bc32716", -- идентификатор заказа 1С
        "CNT" : 25.0,                                              -- количество отгруженное
        "NOM_NAME" : "OS A42A-43N-32-LZ",-- наименование изделия
        "NOM_SHEET" : "A42A-43NLZ.5424",   -- чертежный номер изделия
        "TRANS_ID" : 273441600,                       -- номер транзакции
        "KOD_IZD_L" : "00-00026782",             -- 1С код изделия вычисленный по коду номенклатуры
        "ITEM_ID_Z" : "3869983",                     -- идентификатор строки заказа
        "PACK_ID" : "354220",                            -- номер партии HiTek
        "ID_ZAKAZ" : "97013",                            --  идентификатор заказа 1С в HiTek
        "QR" : "00-000267822312-0489447002", -- QR - код
        "STR_ID" : 17,                                           -- Склад-получатель
        "STR_OUT_ID" : 1,                                   -- Склад-отправитель
        "ERP_DEPARTMENT_REF" : "00000000-0000-0000-0000-000000000000", -- Подразделение ERP
         "FROM_C1" : false,                                -- Заказ из "Прибора"
        "C1_TRANS_IMP_ID" : 296552            -- идентификатор строки
    }
}