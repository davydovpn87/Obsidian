

// Проверка на уволенного сотрудника
        if(context.Iniciator.Status  != UserStatus.Active)
        {
          var zam = EntityManager<UvolennyeSotrudniki>.Instance.Find(x => x.Sotrudnik == context.Iniciator).First();
                    if (zam == null  zam.Sotrudnik == null  zam.Sotrudnik.Status != UserStatus.Active)
                    {
            throw new Exception("Не найден замещающий для уволенного сотрудника");
                    }
          PublicAPI.Processes.WorkflowInstance.AddComment(context.WorkflowInstance, "Инициатор заблокирован. Новый ответственный за процесс: " + zam.Sotrudnik.FIO);
          context.Iniciator = zam.Otvetsvennyy;
        }
    
    private User GetReplacer(Context context, User employee)
    {
      try
      {
        if (employee.EmployeeStatus == EOEmployeeStatus.Active)
        {
          return employee;
        }
        var dismissed = EntityManager<UvolennyeSotrudniki>.Instance
              .Find(x => x.Sotrudnik == employee && x.Otvetsvennyy != null && x.Sotrudnik != x.Otvetsvennyy)
              .OrderByDescending(x => x.Id).FirstOrDefault();
        if (dismissed == null  dismissed.Otvetsvennyy == null)
        {
          return null;
        }
        return GetReplacer(context, dismissed.Otvetsvennyy);
      }
      catch (Exception e)
      {
        TekoStatic.WorkflowLog(context.WorkflowInstance, "WF_Id : " + context.WorkflowInstance.Id + "\n" + e.ToString(), true);
        throw e;
      }
    }
  
  
  private User GetReplacer(Context context, User employee)
    {
      try
      {
        if (employee.EmployeeStatus == EOEmployeeStatus.Active)
        {
          return employee;
        }
        var dismissed = EntityManager<UvolennyeSotrudniki>.Instance
              .Find(x => x.Sotrudnik == employee && x.Otvetsvennyy != null && x.Sotrudnik != x.Otvetsvennyy)
              .OrderByDescending(x => x.Id).FirstOrDefault();
        if (dismissed == null  dismissed.Otvetsvennyy == null)
        {
          return null;
        }
        return GetReplacer(context, dismissed.Otvetsvennyy);
      }
      catch (Exception e)
      {
        TekoStatic.WorkflowLog(context.WorkflowInstance, "WF_Id : " + context.WorkflowInstance.Id + "\n" + e.ToString(), true);
        throw e;
      }
    }
  
  
  
  /// <summary>
    /// * Метод определяет проверяет уволен ли сотрудник, если уволен, берет его замещающего
    /// </summary>
    private User ScriptGetReplacer (Context context, User employee)
    {
      try {
        if (employee.EmployeeStatus == EOEmployeeStatus.Active) {
          return employee;
        }
        var dismissed = EntityManager<UvolennyeSotrudniki>.Instance.Find (x => x.Sotrudnik == employee && x.Otvetsvennyy != null && x.Sotrudnik != x.Otvetsvennyy).OrderByDescending (x => x.Id).FirstOrDefault ();
        if (dismissed == null || dismissed.Otvetsvennyy == null) {
          return null;
        }
        return ScriptGetReplacer (context, dismissed.Otvetsvennyy);
      }
      catch (Exception e) {
        TekoStatic.WorkflowLog (context.WorkflowInstance, e.ToString (), true);
        throw e;
      }
    }











- [ ] Дима Нужно добавить метод IsInOrgItem(long userId, long ogrItemId) в TekoStatic и 
- [ ] /*- переписать на текостатик модели 
- P_LabIspytaniya_Scripts	5092 ---
P_Peredelka_Scripts	4840 -- Переделка
P_Peremarkirovka_Scripts	3580 ---NomRequest
P_ProizvestiOsnastku_Scripts	3520      ----BomQuantityRequest
P_Reklamacii_Scripts	5143						----ПерепроверкаRequest
P_UregulirovatjRashozhdeni_Scripts	4461 ---- ProdOrderAgreed
P_UstanovochnayaPartiya_Scripts	5009     ---- BomQuantityRequest
P_ZakazatjKomplektuyuschie_Scripts	3226 ---- NomQuantityRequest
P_ZapuskOpytnoyPartiiVProi_Scripts	4973 ---- PrototypeOrder*/
- [ ]   TekoStatic.GetAllStatusUsersByGroup
- [ ] IsInGroup


/// <summary>
		/// * Метод определяет проверяет уволен ли сотрудник, если уволен, берет его замещающего
		/// </summary>
		private User ScriptGetReplacer (Context context, User employee)
		{
			try {
				if (employee.EmployeeStatus == EOEmployeeStatus.Active) {
					return employee;
				}
				var dismissed = EntityManager<UvolennyeSotrudniki>.Instance.Find (x => x.Sotrudnik == employee && x.Otvetsvennyy != null && x.Sotrudnik != x.Otvetsvennyy).OrderByDescending (x => x.Id).FirstOrDefault ();
				if (dismissed == null || dismissed.Otvetsvennyy == null) {
					return null;
				}
				return ScriptGetReplacer (context, dismissed.Otvetsvennyy);
			}
			catch (Exception e) {
				TekoStatic.WorkflowLog (context.WorkflowInstance, e.ToString (), true);
				throw e;
			}
		}


### json 
```
{
  "type": "Elma.Справочник.Партнер.Целевой",
  "value": {
    "Ref": "9337ef86-b4bc-11ec-8437-ac1f6bc32716",
    "Целевой": false,
    "Т_ТипКлиента": "31848b1c-6a74-11ea-8429-ac1f6bc32716",
    "Т_УчастиеВЭТП": false,
    "ОсновнойМенеджер": "eb70a8dd-e093-11eb-8434-ac1f6bc32716",
    "Т_Потенциал": 400000,
    "ДатаОтправки": "2025-03-28T16:30:29"
  }
}
```

### GetBossByOrgItem + GetBossOtdelByOrgItem
```
public virtual User GetBossByOrgItem(OrganizationItem OrgItem)
    {
      if (OrgItem == null)
      {
        return null;
      }
      OrganizationItem childOrgItem = (OrgItem.ItemType == OrganizationItemType.Department) ? OrgItem.SubItems.FirstOrDefault() : OrgItem;
      if (childOrgItem != null && childOrgItem.User != null)
      {
        return childOrgItem.User;
      }
      else
      {
        return GetBossByOrgItem(OrgItem.ParentItem);
      }
    }
    public virtual OrganizationItem GetBossOtdelByOrgItem(OrganizationItem OrgItem)
    {
      if (OrgItem.ItemType == OrganizationItemType.Department)
      {
        return OrgItem;
      }
      else
      {
        return GetBossOtdelByOrgItem(OrgItem.ParentItem);
      }
    }
```





```
private string GetProcessUrl (WorkflowInstance instance)  
        {  
            string baseUrl = Locator.GetServiceNotNull<CommonSettingsModule> ().Settings.ApplicationBaseUrl;  
            return string.Format ("{0}/Processes/WorkflowInstance/Info/{1}", baseUrl, instance.Id);  
        }
```

```
private void ScriptSetEntityFilter<T> (Context context, Expression<Func<Context, T>> expression, string eqlFilter = "") { try { var fieldSettings = context.GetSettingsFor (expression).As<EntitySettings> (); fieldSettings.FilterQuery = eqlFilter; [fieldSettings.Save](http://fieldSettings.Save) (); } catch (Exception e) { TekoStatic.Log ("WF_Id: " + [context.WorkflowInstance.Id](http://context.WorkflowInstance.Id) + "\n" + e.ToString (), true, ScriptGetLogFileName (context)); } }

Применение

ScriptSetEntityFilter (context, x => x.Zadolzhennostj, "IsDeleted <> 1");
```

```
private void WorkflowLog(Workflow.Models.WorkflowInstance instance, string text, bool isAlarm) { TekoStatic.Log(string.Format("Процесс_{0} {1}\n{2}", [instance.Process.Header.Name](http://instance.Process.Header.Name), [instance.Id](http://instance.Id), text), isAlarm, [instance.Process.Header.Name](http://instance.Process.Header.Name)); }

применение WorkflowLog(context.WorkflowInstance, e.ToString(), true); вместо

TekoStatic.Log ("WF_Id: " + [context.WorkflowInstance.Id](http://context.WorkflowInstance.Id) + "\n" + e.ToString (), true, ScriptGetLogFileName (context));
```

```
public class CommentToERP  
        {  
            [JsonConverter (typeof(DateTimeConverterForErpHashaForma))]  
            public DateTime ДатаОтправки;  
  
            public Guid? Ref;  
  
            public Guid WorkflowInstance;  
  
            public string Text = string.Empty;  
  
            public string Link = "";  
        }
        
public class DateTimeConverterForErpHashaForma : IsoDateTimeConverter  
        {  
            public DateTimeConverterForErpHashaForma ()  
            {  
                //2024-06-26T16:58:03  
                base.DateTimeFormat = "yyyy-MM-ddTHH:mm:ss";  
            }  
        }
```

