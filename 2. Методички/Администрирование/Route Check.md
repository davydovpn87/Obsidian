  #SQL
GRANT ALL PRIVILEGES ON DATABASE route_check_site to devl;  
GRANT CONNECT ON DATABASE route_check_site TO devl;  
GRANT USAGE ON SCHEMA public TO devl;  
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO devl;  
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO devl;  

CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

drop table routechecktransaction;  
  
create table routechecktransaction  
(  
   id bigserial,  
   uid uuid,  
   operation bigint,  
   employee bigint,  
   is_output bool,  
   quantity double precision,  
   creation_date timestamp without time zone,  
   modify_date timestamp without time zone,  
   send_date timestamp without time zone,  
   synchonized bool,  
   tech_info text  
);  
create unique index table_name_id_uindex on routechecktransaction (id);  
create unique index table_name_uid_uindex on routechecktransaction (uid);  
alter table routechecktransaction add constraint routechecktransaction_pk primary key (id);  
alter table routechecktransaction add constraint fk_routechecktransaction_employee foreign key (employee) REFERENCES ht_users(id);  
alter table routechecktransaction add constraint fk_routechecktransaction_operation foreign key (operation) REFERENCES ht_marshrutnyyyarlykoperaciy(id);  
--  
alter table ht_users add constraint ht_users_pk primary key (id);  
alter table ht_marshrutnyyyarlykoperaciy add constraint ht_marshrutnyyyarlykoperaciy_pk primary key (id);  
alter table ht_marshrutnyyyarlyk add constraint ht_marshrutnyyyarlyk_pk primary key (id);  
--  
alter table ht_marshrutnyyyarlykoperaciy add constraint fk_ht_marshrutnyyyarlyk  
    foreign key (marshrutnyyyarlyk) REFERENCES ht_marshrutnyyyarlyk(id);


create index ht_marshrutnyyyarlyk_nomer_index  on ht_marshrutnyyyarlyk (nomer);

create index ht_marshrutnyyyarlykoperaciy_marshrutnyyyarlyk_index  
   on ht_marshrutnyyyarlykoperaciy (marshrutnyyyarlyk desc);


  
CREATE FUNCTION public."NotifyOnDataChange"()  
  RETURNS trigger  
  LANGUAGE 'plpgsql'  
AS $BODY$  
DECLARE  
  data JSON;  notification JSON;  
BEGIN  
  -- if we delete, then pass the old data  
  -- if we insert or update, pass the new data  IF (TG_OP = 'DELETE') THEN  
    data = row_to_json(OLD);  
  ELSE    data = row_to_json(NEW);  
  END IF;  -- create json payload  
  -- note that here can be done projection  notification = json_build_object(  
            'table',TG_TABLE_NAME,  
            'action', TG_OP, -- can have value of INSERT, UPDATE, DELETE  
            'data', data);  
    -- note that channel name MUST be lowercase, otherwise pg_notify() won't work  
    PERFORM pg_notify('datachange', notification::TEXT);  
  RETURN NEW;END  
$BODY$;  
  
  
CREATE TRIGGER "OnDataChangeInRCTransaction"  
  AFTER INSERT  
  ON public.routechecktransaction  
  FOR EACH ROW  
  EXECUTE PROCEDURE public."NotifyOnDataChange"();


#RABBIT
RoutineCheckSite - queue
ERP.Справочник.Номенклатура
HT.Документ.МаршрутныйЯрлык
HT.Документ.МаршрутныйЯрлык.Операция
HT.Справочник.Сотрудники

From_RoutineCheckSite - exchange
RCS.Справочник.Транзакция




















#trigger
  
```
``` 
CREATE FUNCTION public."NotifyOnDataChange"()  
  RETURNS trigger  
  LANGUAGE 'plpgsql'  
AS $BODY$  
DECLARE  
  data JSON;  notification JSON;  
BEGIN  
  -- if we delete, then pass the old data  
  -- if we insert or update, pass the new data  IF (TG_OP = 'DELETE') THEN  
    data = row_to_json(OLD);  
  ELSE    data = row_to_json(NEW);  
  END IF;  -- create json payload  
  -- note that here can be done projection  notification = json_build_object(  
            'table',TG_TABLE_NAME,  
            'action', TG_OP, -- can have value of INSERT, UPDATE, DELETE  
            'data', data);  
    -- note that channel name MUST be lowercase, otherwise pg_notify() won't work  
    PERFORM pg_notify('datachange', notification::TEXT);  
  RETURN NEW;END  
$BODY$;  
  
  
CREATE TRIGGER "OnDataChangeInRCTransaction"  
  AFTER INSERT  
  ON public.routechecktransaction  
  FOR EACH ROW  
  EXECUTE PROCEDURE public."NotifyOnDataChange"();

  
  
  
  #StoredProgs
----------------  
-- no generators  
  
----------------  
CREATE OR REPLACE FUNCTION teko_ht_users(_ht_id bigint, _first text, _middl text,  
    _last text, _sn double precision, _sn_in double precision, _isphoto integer, _del integer, _json text)  
RETURNS void AS $BODY$  
    DECLARE _thisId integer := (select id from public.ht_users where ht_id = _ht_id limit 1);  
BEGIN   IF (_thisId is not null)  
        THEN update  public.ht_users  
            set first_name = _first, middle_name = _middl, last_name = _last, sn = _sn, sn_int = _sn_in,  
                is_photo = _isphoto, is_deleted = _del, origjson = _json, sys_modify_date = now()  
            where public.ht_users.ht_id = _ht_id;  
        ELSE INSERT INTO  ht_users (id, uid, ht_id, first_name, middle_name, last_name,  
                    sn, sn_int, is_photo, is_deleted, origjson, sys_create_date, sys_modify_date)  
           values ((select max(id) + 1 from ht_users), uuid_generate_v4(), _ht_id, _first, _middl, _last,  
                  _sn, _sn_in, _isphoto, _del, _json, now(), now() );  
        END IF;END; $BODY$ LANGUAGE 'plpgsql' VOLATILE COST 100;  
  
----------------  
-- no ex1c_zakaznaperemeschenie  
-- no zakazklienta  
create or replace function teko_ht_routinelabel  
    (_num integer,_pack_id integer,_pack_route_id integer,_cnt double precision,_date_c timestamp without time zone,_status integer,  
                _zakaz_num text, _c1_zakaz uuid, _c1_ref uuid,_nom_id integer,_nom_name text,_obj_name text, _zakaz integer)  
RETURNS void AS $BODY$  
    DECLARE _thisId integer := (select id from public.ht_marshrutnyyyarlyk where idmarshrutnogoyarlyka = _pack_route_id limit 1);  
BEGIN IF (_thisId is not null)  
      THEN  update  public.ht_marshrutnyyyarlyk  
            set  nomer = _num, nomerpartii = _pack_id, kolichestvo = _cnt, datasozdaniya = _date_c,  
                status = _status, nomerpartii1s = _zakaz_num, zakaznaperemeschenieref = _c1_zakaz,  
                zakaznaperemeschenie = null,  
                guidnomenklatury1s = _c1_ref, idnomenklaturyhitek = _nom_id, naimenovanienomenklatury = _nom_name, vidnomenklatury = _obj_name,  
                naimenovanie =  _num || coalesce(' ' || replace(_nom_name, '  ', ' ') ,'') || coalesce(' ' || _zakaz_num,''),  
                nomenklatura = (select id from ex1c_nomenclature where "Ref" = _c1_ref), nomerzakazavproizvodstvo = _zakaz  
            where idmarshrutnogoyarlyka = _pack_route_id;  
      ELSE  INSERT INTO public.ht_marshrutnyyyarlyk(id, uid, nomer, nomerpartii, idmarshrutnogoyarlyka, kolichestvo, datasozdaniya,  
                status, nomerpartii1s, zakaznaperemeschenieref, zakaznaperemeschenie, guidnomenklatury1s, idnomenklaturyhitek,  
                naimenovanienomenklatury, vidnomenklatury, naimenovanie, nomenklatura, nomerzakazavproizvodstvo)  
            values ((select max(id) + 1 from ht_marshrutnyyyarlyk), uuid_generate_v4(),_num,_pack_id,_pack_route_id,_cnt,_date_c,  
                    _status,_zakaz_num,_c1_zakaz, null,  
                    _c1_ref,_nom_id,_nom_name,_obj_name, '(' || _num || ') ' || ' - ' || coalesce(_nom_name,'') || coalesce(_zakaz_num,''),  
                    (select id from ex1c_nomenclature where "Ref" = _c1_ref), _zakaz);  
END IF;  
END; $BODY$ LANGUAGE 'plpgsql' VOLATILE COST 100;  
create index IF NOT EXISTS ht_marshrutnyyyarlyk_idmarshrutnogoyarlyka_index on ht_marshrutnyyyarlyk (idmarshrutnogoyarlyka);  
  
-------------  
-- no sotrudnikelma  
create or replace function teko_ht_routinelabel_oper  
    (_pack_route_id integer,_route_order integer,_label_id integer,_cnt_in double precision,_cnt_out double precision,  
    _cnt_def double precision, _cnt_rep double precision,_date_b timestamp without time zone,_date_e timestamp without time zone,  
    _empl integer,_descr text,_oper_name text,_label_d text, _defect_name text, _ins_oper text, _is_del integer)  
RETURNS void AS $BODY$ BEGIN  
IF (select count(*) from  public.ht_marshrutnyyyarlykoperaciy where idoperacii = _pack_route_id) > 0  
    THEN  update  public.ht_marshrutnyyyarlykoperaciy  
       set  poryadkovyynomer = _route_order, idmarshrutnogoyarlyka = _label_id,  
            kolichestvovhod = _cnt_in, kolichestvovyhod = _cnt_out, kolichestvobrak = _cnt_def, otremontirovano = _cnt_rep,  
           datanachala = (case when extract(year from _date_b) = 1 then null else _date_b end ),  
           dataokonchaniya = (case when extract(year from _date_e) = 1 then null else _date_e end ),  
           sotrudnik = _empl, opisanie = _descr, ceh = _oper_name, opisaniekratko = _label_d,  
           sotrudnikelma = null, defect = _defect_name, defectcomment = _ins_oper, isdeleted = _is_del  
       where idoperacii = _pack_route_id;  
    ELSE  INSERT INTO public.ht_marshrutnyyyarlykoperaciy(id, uid, idoperacii, poryadkovyynomer, idmarshrutnogoyarlyka,  
            kolichestvovhod, kolichestvovyhod, kolichestvobrak, otremontirovano, datanachala, dataokonchaniya, sotrudnik,  
            opisanie, ceh, opisaniekratko, sotrudnikelma, defect, defectcomment, isdeleted)  
        values (((select max(id) + 1 from ht_marshrutnyyyarlykoperaciy)), uuid_generate_v4(),_pack_route_id,_route_order,_label_id,  
                _cnt_in,_cnt_out,_cnt_def,_cnt_rep,  
                (case when extract(year from _date_b) = 1 then null else _date_b end ),  
                (case when extract(year from _date_e) = 1 then null else _date_e end ),  
                _empl,_descr,_oper_name,_label_d, null, _defect_name, _ins_oper, _is_del);  
END IF; END; $BODY$ LANGUAGE 'plpgsql' VOLATILE COST 100;  
create index IF NOT EXISTS  ht_marshrutnyyyarlykoperaciy_idoperacii_index on ht_marshrutnyyyarlykoperaciy (idoperacii);  
  
---------------  
-- no ex1c_edinicyizmereniya  
-- no ex1c_sklad  
-- no ex1c_vidnomenklatury  
-- no ex1c_proizvoditeli  
CREATE OR REPLACE FUNCTION teko_1c_nomenclature(_isgroup integer, _deletionmark integer, _ref uuid, _parentuid uuid, _code text,  
    _description text, _naimenovaniepolnoe text, _artikul text, _stavkands text, _opisanie text, _tipnomenklatury text, _file uuid,  
    _edizm uuid, _t_tovarprodukciya integer, _t_seriynyyzakaznoy integer, _t_uchetseriynyhnomerov integer, _t_trebuetsyapereproverka integer,  
    _t_pereproveryatjvsegda integer, _t_srokhraneniyavdnyah bigint, _t_obscheprom integer, _t_nomernz text, _t_minimaljnayapartiya double precision,  
    _t_srokpostavki bigint, _t_strahovoyzapas double precision, _t_popolnenie double precision, _t_storonniy integer, _t_mozhemproizvoditj integer,  
    _obiskladhraneniya uuid, _obikotel integer, _obipolufabrikat integer, _t_snyatsproizvodstva integer,  
    _t_datasnyatiyasproizvodstva timestamp without time zone, _t_analog uuid, _t_analogotsutstvuet integer, _li_datasozdaniya timestamp without time zone,  
    _jsonstring text, _t_minimaljnayapartiyazapuska double precision, _vid_nom uuid, _proizvoditel uuid)  
RETURNS void AS $BODY$ BEGIN  
IF (select count(*) from  public.ex1c_nomenclature where public.ex1c_nomenclature."Ref" = _ref ) > 0  
  THEN  
     update  public.ex1c_nomenclature  
     set isgroup = _isgroup, deletionmark = _deletionmark, parentuid = _parentuid,  
        code = _code, description = _description, naimenovaniepolnoe = _naimenovaniepolnoe, artikul = _artikul,  
        stavkands = _stavkands, opisanie = _opisanie, tipnomenklatury = _tipnomenklatury, PasportV1s = _file,  
         edinicaizmereniy = null,  
         edinicaizmereniyaref = _edizm, changedate = now(), t_tovarprodukciya = _t_tovarprodukciya,  
          t_seriynyyzakaznoy = _t_seriynyyzakaznoy, t_uchetseriynyhnomerov = _t_uchetseriynyhnomerov,  
         t_trebuetsyapereproverka = _t_trebuetsyapereproverka, t_pereproveryatjvsegda = _t_pereproveryatjvsegda,  
          t_srokhraneniyavdnyah = _t_srokhraneniyavdnyah, t_obscheprom = _t_obscheprom, t_nomernz =_t_nomernz,  
         t_minimaljnayapartiya = _t_minimaljnayapartiya, t_srokpostavki = _t_srokpostavki, t_strahovoyzapas = _t_strahovoyzapas,  
          t_popolnenie =_t_popolnenie, t_storonniy =_t_storonniy, t_mozhemproizvoditj =_t_mozhemproizvoditj,  
         obiskladhraneniya =null, obikotel =_obikotel, obipolufabrikat = _obipolufabrikat,  
          t_snyatsproizvodstva = _t_snyatsproizvodstva, t_datasnyatiyasproizvodstva =_t_datasnyatiyasproizvodstva,  
         t_analog = (select id from ex1c_nomenclature where "Ref" = _t_analog limit 1), t_analogotsutstvuet =_t_analogotsutstvuet,  
         li_datasozdaniya =_li_datasozdaniya, jsonstring =_jsonstring, t_minimaljnayapartiyazakaza = _t_minimaljnayapartiyazapuska,  
         vidnomenklatury = _vid_nom, vidnomenklaturid = null,  
         proizvoditelj = null  
     where public.ex1c_nomenclature."Ref" = _ref;  
  ELSE      INSERT INTO  public.ex1c_nomenclature(id, uid, isgroup, "Ref", parentuid, deletionmark, code, description,  
            naimenovaniepolnoe, artikul, stavkands, opisanie, tipnomenklatury, pasportv1s,  
            edinicaizmereniyaref, edinicaizmereniy, changedate, t_tovarprodukciya,  
            t_seriynyyzakaznoy, t_uchetseriynyhnomerov, t_trebuetsyapereproverka, t_pereproveryatjvsegda,  
            t_srokhraneniyavdnyah, t_obscheprom, t_nomernz, t_minimaljnayapartiya, t_srokpostavki, t_strahovoyzapas,  
            t_popolnenie, t_storonniy, t_mozhemproizvoditj, obiskladhraneniya, obikotel, obipolufabrikat,  
            t_snyatsproizvodstva, t_datasnyatiyasproizvodstva, t_analog, t_analogotsutstvuet, li_datasozdaniya, jsonstring,  
            t_minimaljnayapartiyazakaza, vidnomenklaturid, vidnomenklatury, proizvoditelj)  
     select (select max(id) + 1 from public.ex1c_nomenclature) , uuid_generate_v4() ,  
          _isgroup, _ref, _parentuid, _deletionmark, _code, _description,  
          _naimenovaniepolnoe, _artikul, _stavkands, _opisanie, _tipnomenklatury, _file,  
            _edizm, null, now(), _t_tovarprodukciya,  
            _t_seriynyyzakaznoy, _t_uchetseriynyhnomerov, _t_trebuetsyapereproverka, _t_pereproveryatjvsegda,  
            _t_srokhraneniyavdnyah, _t_obscheprom, _t_nomernz, _t_minimaljnayapartiya, _t_srokpostavki, _t_strahovoyzapas,  
            _t_popolnenie, _t_storonniy, _t_mozhemproizvoditj, null, _obikotel, _obipolufabrikat,  
            _t_snyatsproizvodstva, _t_datasnyatiyasproizvodstva, (select id from ex1c_nomenclature where "Ref" = _t_analog limit 1),  
            _t_analogotsutstvuet, _li_datasozdaniya, _jsonstring,  
            _t_minimaljnayapartiyazapuska, null, _vid_nom, null;  
end if;  
update public.ex1c_nomenclature set parent = (select id from public.ex1c_nomenclature pp  
    where pp."Ref" != '00000000-0000-0000-0000-000000000000' and  pp."Ref" = public.ex1c_nomenclature.parentuid) where "Ref" = _ref;  
 END; $BODY$ LANGUAGE 'plpgsql' VOLATILE COST 100;  
DO $$ BEGIN  
IF to_regclass('public.ex1c_nomenclature_ref_index_2') IS NULL THEN create index ex1c_nomenclature_ref_index_2 on ex1c_nomenclature ("Ref");  
END IF; END $$;