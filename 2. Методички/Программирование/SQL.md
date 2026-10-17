
### 1. Admin. UseFull
#### Таблицы с исходным кодом процессов, отчетов, шаблонов Елмы
```
select * from scriptmodule where  sourcecode ilike '%pagevisit%'  
select * from viewtemplate where "Template" ilike '%pagevisit%'  
select * from reportdatasource where query ilike '%pagevisit%'
```
#### Комментарии в обновлении
```
update "Comment" set "Text" = '' where "Text" = 'Обновление';
update "Comment" set "Text" = '' where "Text" = 'Плановое обновление';

#### Гранты Метабейсу
GRANT SELECT ON ALL TABLES IN SCHEMA public TO metabase;
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public TO metabase;

#### На проде вот так надо брать время
select now() AT TIME ZONE 'posix/Asia/Yekaterinburg' -- да
(select now()::timestamptz AT TIME ZONE 'GMT-5' val) currentdate -- нет

### Получить имя сервера
left join systemsettings ss on ss.settingkey like '%ApplicationBaseUrl%'  

### Получить перенос каретки
left join (select value enter from teko_simbol where id = 1 ) as  const on true

###  Все сотрудники по группам и должностям
select * from usersecuritysetcache
```





###  2. Запрос в ОИТ

```
#### Беклог. По эпикам
select epik.nazvanie,  count(*)
from oit_zadachi t
    left join workflowinstance w on t.process = w.id
    left join p_zaprosvtehpodderzhku p on w.id = p.workflowinstance
    left join oit_servis s on p.servis = s.id
    left join epik on t.epik = epik.id
where w.status = 1
    and s.id = 1
    --and t.status = 'BackLog'
group by 1 order by 2 desc;
```

```
#### Беклог. По эпикам
select stadiya.nazvanie,  count(*)  
from oit_zadachi t  
    left join workflowinstance w on t.process = w.id  
    left join p_zaprosvtehpodderzhku p on w.id = p.workflowinstance  
    left join oit_servis s on p.servis = s.id  
    left join stadiya on t.stadiya = stadiya.id  
where w.status = 1  
    and s.id = 1  
group by 1 order by 2 desc;
```

```
####  Плохие оценки
select u_i.fio "Инициатор",
       u_o.fio "Обративщийся", ocenka ,
       u_p.fio "Получатель", ocenkapoluchatelyauslugi  ,
       wi.id
from P_ZaprosVTehPodderzhku p
    left join workflowinstance wi on p.workflowinstance = wi.id
    left join "User"  u_i on p.iniciator = u_i.id
    left join "User"  u_o on p.obrativshiysya = u_o.id
    left join "User"  u_p on p.poluchateljuslugi = u_p.id
    left join oit_servis os on os.id = p.servis
    left join "User" u_serv on u_serv.id  = os.otvetstvennyy
where   --u_serv.id = 150
        --u_serv.id = 5
        u_serv.id = 482
        --u_serv.id = 111
and (ocenka != 'Отлично' or ocenkapoluchatelyauslugi != 'Отлично')
and wi.enddate > '2025-01-01';
```

```
####  Задачи подтверждения
select  u.fio,   count(*)
from workflowbookmark wb
    left join taskbase t on wb.id = t.workflowbookmark
    left join workflowinstance wi on wb.instance = wi.id
    left join P_ZaprosVTehPodderzhku p on wi.id = p.workflowinstance
    left join "User" u on t.executor = u.id
where wi.status = 1
  and (wb.elementuid = 'e9b279e3-672b-4e1f-bcf2-c2d68f2fe7b0' or wb.elementuid = 'f2503830-db7f-49d8-a2f0-b4361c456e5a' )
    and p.servis = 1
  and t.endworkdate is null

group by 1 order by 2 desc
```


```
select u.fio,  
       round(cast (p_now.itogovmesyacnaruki/1000 as numeric), 0),  
       round(cast (p_ask.itogovmesyacnaruki/1000 as numeric), 0)  
from P_IzmenitjZP_BlokIzmenenieZP p_b  
    left join "User" u on p_b.sotrudnik = u.id  
    left join P_IzmenitjZP_DeystvuyuschiyF p_now on p_b.id = p_now.parent  
    left join P_IzmenitjZP_BlokProektFOT p_ask on p_b.id = p_ask.parent  
order by p_b.id desc  
;
```

### 3. ЭДО
```
#### Текущий статус контрагента
select u.fio, d.naimenovanie, mlp."Name",
       (select string_agg('https://elma.teko-com.ru/Processes/WorkflowInstance/Info/' || p.workflowinstance, ' ')
            from P_InvitationToEDM p where p.kontragent = c.id),
       c.code, c."Name"
from
     (select ces_in.contractor, max(id) max_id
        from contractoredostatus ces_in
     group by ces_in.contractor) base
    left join contractoredostatus ces on base.max_id = ces.id
    left join contractor c on ces.contractor = c.id
    left join mylegalperson mlp on ces.mylegalperson = mlp.id
    left join "User" u on u.id = c.responsible
    left join diadoccontractorstatus d on ces.diadocstatus = d.id
order by u.fio, d.naimenovanie, mlp."Name", c."Name"
```

### 5. Проверки
```
# Grouping tasks елем иды и имена тасков на них
select ph.id,  
    ph."Name" as processName,  
    wti.elementuid as elementUid,  
    string_agg( tb.subject, ', ') as taskName  
from processheader ph  
    left join groupingtasks gt on gt.processheader = ph.id  
    left join workflowprocess wp on wp."Header" = ph.id  
    left join workflowinstance wi on wi.process = wp.id  
    left join workflowbookmark wb on wb."instance" = wi.id  
    left join taskbase tb on tb.workflowbookmark = wb.id  
    left join workflowtrackingitem wti on wti."instance" = wi.id  
    left join groupingtasks_elements ge on ge.elementuid = wti.elementuid  
    left join celjprocessa on ph.id = celjprocessa.zaolovokprocessa  
where gt.title isnull  
    and wti.elementuid is not null  
    and celjprocessa.sistemnyy = 0  
group by 1, 2, 3;


### Получить все зоны ответствености с группой "все пользователи"
with max_ids as (
    select uid, max(id) max_id
        from md_entity_property
    group by 1),
    property as  (
        select md.id, md."Name", md.displayname, md.ownerid,  encode(md.settings, 'escape') sett
        from md_entity_property md
        where md.id in (select max_id from max_ids)
    )

select md_entity.displayname,   property.displayname, md_entity.tablename
from property
    left join md_entity on md_entity.id = property.ownerid
where property.sett ilike '%&lt;WorkerId&gt;2&lt;/WorkerId&gt;%'
and property.displayname != 'Инициатор'
order by 1, 2;

with max_ids as (  
    select uid, max(id) max_id  
        from md_entity_property  
    group by 1),  
    property as  (  
        select md.id, md."Name", md.displayname, md.ownerid,  encode(md.settings, 'escape') sett  
        from md_entity_property md  
        where md.id in (select max_id from max_ids)  
    )  
select md_entity.displayname,   property.displayname, md_entity.tablename, ph."Name"  
from property  
    left join md_entity on md_entity.id = property.ownerid  
    left join scriptmodule on classname ilike  '%'||md_entity.tablename||'%'  
    left join workflowprocess on scriptmodule.objectuid = workflowprocess.uid  
    left join processheader ph on workflowprocess."Header" = ph.id  
where property.sett ilike '%&lt;WorkerId&gt;2&lt;/WorkerId&gt;%'  
and property.displayname != 'Инициатор'  
group by 1, 2, 3, 4  
order by md_entity.tablename;
```


###  7. WorkflowInstance. MetaTables
```


###  Получить все переменные из контекста
select "Name", displayname , count(*)
from md_entity_property
where ownerid in (select id from md_entity where tablename = 'P_SZPoKnopkeMarketing')
group by "Name", displayname;			


### Рефакторинг процесса по договорам
select * from processheader where "Name" ilike '%форма клие%'

-- on close
select split_part(res.res, '</ScriptName>', 1), split_part( split_part(res.res, '</ScriptName>', 1), '(', 1)
from
    (select splitstring(  encode(diagram,'escape')  , '<ScriptName>' ) res
    from workflowprocess where "Header" = 39 and id = 4145
    ) as res
where res.res ilike '%</ScriptName>%' and split_part(res.res, '</ScriptName>', 1)   ilike '%FormViewBuilder%'
    and split_part(res.res, '</ScriptName>', 1)  not ilike '%GatewayVar%'
group by 1 order by 1;

-- Получение всех onMap + условия на задачах
select split_part(res.res, '</ScriptName>', 1), split_part( split_part(res.res, '</ScriptName>', 1), '(', 1)
from
    (select splitstring(  encode(diagram,'escape')  , '<ScriptName>' ) res
    from workflowprocess where "Header" = 39 and id = 4145
    ) as res
where res.res ilike '%</ScriptName>%' and split_part(res.res, '</ScriptName>', 1) not  ilike '%FormViewBuilder%'
    and split_part(res.res, '</ScriptName>', 1)  not ilike '%GatewayVar%'
group by 1 order by 1;

-- Onchoose
select split_part(res.res, '</ScriptName>', 1), split_part( split_part(res.res, '</ScriptName>', 1), '(', 1)
from
    (select splitstring(  encode(diagram,'escape')  , '<ScriptName>' ) res
    from workflowprocess where "Header" = 39 and id = 4145
    ) as res
where res.res ilike '%</ScriptName>%' and split_part(res.res, '</ScriptName>', 1) not  ilike '%FormViewBuilder%'
    and split_part(res.res, '</ScriptName>', 1)   ilike '%GatewayVar%'
group by 1 order by 1;

-- Получение всех onLoad
select split_part(res.res, '</OnLoadScriptName>', 1)
from
    (select splitstring(  encode(diagram,'escape')  , '<OnLoadScriptName>' ) res
    from workflowprocess where "Header" = 39 and id = 4145
    ) as res
where res.res ilike '%OnLoadScriptName%'
group by 1;

```




### 8. Integration
```
### Ошибка Создали контрагентов - не создали contractoruids

select p.workflowinstance, kontragentref, wi.startdate  
from P_IzmenitjKontragenta p  
    left join workflowinstance wi on p.workflowinstance = wi.id  
left join ex1c_contractoruids e1cc on p.kontragentref = e1cc.ref  
where e1cc.id is null and kontragentref is not null  ;
--11d7683f-c758-46f3-acc4-f9a62a94555e нет такой записи в ex1c_contractoruids
```



### 9. Recursive
```
### Получить оргструктуру компании teko_orgstructure
with recursive teko_orgstructure as (  
    select o.id orgitem_id, o."Name" orgitem_name, array[o.id] as path, coalesce(dep."Name",'') department, coalesce(dep."Name",null  )  maindepartment,  
           array_remove(CAST ( o.userid  AS text ) || string_to_array(o.usershash, ', '), null) users  
    from public.organizationitem o  
        left join public.organizationitem dep on o.parentitem = dep.id and dep.itemtype = 0  
    where o.id = 148 union  
    select o.id orgitem_id, o."Name" orgitem_name, p.path || o.id path, coalesce(dep."Name",p.department  ) department,  
           case when  p.maindepartment is null or p.maindepartment = 'Компания' or  p.maindepartment =  'Производственный контур'  
                        or p.maindepartment = 'Служба технического директора'  
               then  coalesce(dep."Name",null  ) else p.maindepartment end dep1,  
           array_remove(CAST ( o.userid  AS text ) || string_to_array(o.usershash, ', '), null) users  
    from public.organizationitem o  
        left join public.organizationitem dep on o.parentitem = dep.id and dep.itemtype = 0  
        left join public.organizationitem dep1 on o.parentitem = dep1.id and dep1.itemtype = 0  
        join teko_orgstructure p on o.parentitem = p.orgitem_id)  
select * from teko_orgstructure order by path;

 # Получить путь оргструктуры
with recursive pktp as (
    select o.id, o."Name", array[o.id] as path, array[case when o.shortname is null then o."Name" else o.shortname end] as path_name, o.itemtype
        from public.organizationitem o where o.id = 433
    union
    select o.id, o."Name", p.path || o.id path,
           case when o.itemtype = 0
               then p.path_name || array[case when o.shortname is null then o."Name" else o.shortname end]
               else p.path_name end as path_name, o.itemtype
    from public.organizationitem o
        join pktp p on o.parentitem = p.id
)
```

