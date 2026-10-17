# Правила выгрузки звонков из Битрикс24

## 1. Таблицы 

| Сущность | Таблица | Назначение |
|---|---|---|
| Звонок | `b_crm_act` | Основной источник: `TYPE_ID=2`, `START_TIME`, `DIRECTION`, `RESPONSIBLE_ID`, `OWNER_ID`, `OWNER_TYPE_ID`, `SUBJECT` |
| Пользователь | `b_user` | ФИО ответственного (`NAME`, `LAST_NAME`, `SECOND_NAME`) и `UF_DEPARTMENT` |
| Подразделение | `b_iblock_section` | Название подразделения по `ID` из `UF_DEPARTMENT` |
| Компания | `b_crm_company` | `ID`, `UF_CRM_1625752189` (код партнёра в ERP) |
| Контакт | `b_crm_contact` | `ID`, `NAME`, `SECOND_NAME`, `LAST_NAME`, `POST`, `COMPANY_ID` |
| Сделка | `b_crm_deal` | `ID`, `COMPANY_ID`, `CONTACT_ID` — для резолва через `OWNER=DEAL` |
| Лид | `b_crm_lead` | `ID`, `COMPANY_ID`, `CONTACT_ID` — для резолва через `OWNER=LEAD` |

## 2. Ответственный менеджер звонка

`b_crm_act.RESPONSIBLE_ID` -> `b_user.ID`:

| Данные | Источник |
|---|---|
| ФИО менеджера | `b_user`: `LAST_NAME + ' ' + NAME + ' ' + SECOND_NAME`, `trim` |
| Подразделение менеджера | `b_user.UF_DEPARTMENT[0]` -> `b_iblock_section.NAME` (по `ID`); при пустом `UF_DEPARTMENT` — `0` |
| ID менеджера | `b_crm_act.RESPONSIBLE_ID` |
| ID подразделения менеджера | `b_user.UF_DEPARTMENT[0]` |

## 3. Направление звонка

`b_crm_act.DIRECTION` для звонков (`TYPE_ID=2`):

| Значение | Смысл |
|---|---|
| 1 | Входящее |
| 2 | Исходящее |
| 0 | Не определено |

## 4. Связка с компанией и контактом 

Привязка звонка — `b_crm_act.OWNER_TYPE_ID` + `OWNER_ID` (оба поля `NOT NULL`).

### `OWNER_TYPE_ID`

| ID | Тип |
|---|---|
| 1 | Лид |
| 2 | Сделка |
| 3 | Контакт |
| 4 | Компания |

### Правила резолва компании и контакта по `OWNER_TYPE_ID`

| `OWNER_TYPE_ID` | `company_id` | `contact_id` |
|---|---|---|
| `4` Компания | `OWNER_ID` | пусто (0) |
| `3` Контакт | `b_crm_contact.COMPANY_ID` по `OWNER_ID` (при 0 — пусто) | `OWNER_ID` |
| `2` Сделка | `b_crm_deal.COMPANY_ID` по `OWNER_ID` (при 0 — пусто) | `b_crm_deal.CONTACT_ID` по `OWNER_ID` (при 0 — пусто) |
| `1` Лид | `b_crm_lead.COMPANY_ID` по `OWNER_ID` (при 0 — пусто) | `b_crm_lead.CONTACT_ID` по `OWNER_ID` (при 0 — пусто) |
| `0` / прочее | пусто | пусто |

## 5. Данные компании

Источник — `b_crm_company` по `company_id`:

| Данные | Источник | Логика |
|---|---|---|
| Код партнёра в ERP | `b_crm_company.UF_CRM_1625752189` | По `company_id` |
| Код компании Б24 | `company_id` | Как есть; пусто при `0` |

## 6. Данные контакта 

Источник — `b_crm_contact` по `contact_id`:

| Данные | Поле | Логика |
|---|---|---|
| ID контакта | `b_crm_contact.ID` | Как есть; пусто при `0` |
| ФИО контакта | `LAST_NAME + ' ' + NAME + ' ' + SECOND_NAME` | как есть |

## 7. Данные звонка

| Данные | Поле | Логика |
|---|---|---|
| Дата коммуникации | `b_crm_act.START_TIME` | Формат `d.m.Y` |
| Тема коммуникации | `b_crm_act.SUBJECT` | Как есть (может быть пустым) |
| Вид коммуникации | `b_crm_act.TYPE_ID` | `2 -> 'Звонок'` |
| Тип коммуникации | `b_crm_act.DIRECTION` | `1 -> 'Входящий'`, `2 -> 'Исходящий'` |
