

### TekoStatic
#### TekoStatic Лог в процессе
```
 TekoStatic.WorkflowLog (context.WorkflowInstance, e.ToString (), true);
```

#### TekoStatic Проверка на эмуляцию
 ```
 if (context.WorkflowInstance.IsEmulation) {
TekoStatic.WorkflowLog(context.WorkflowInstance, "Процесс запущен в эмуляции", true);
	//ScriptGenerateTestData (context); }
```

### RabbitMQ
#### RabbitMQ Базовая отправка сообщения в 
```
jsonText = RbStatic.GetMcJsonFromAnObject(new ChangeDogovorEndDate()  
{  
	Ref = context.DogovorIzERP.Ref.Value,  
	ДатаОкончанияДействия = context.DataOkonchaniyaDeystviyaDogovora.Value,  
	Url = string.Format("{0}/Processes/WorkflowInstance/Info/{1}", baseUrl, context.WorkflowInstance.Id)  
}, "ELMA.Документ.ДопСоглашение.ПролонгацияДоговора");  
new RbSent().SentToExchange("From_Elma", "ELMA.Документ.ДопСоглашение.ПролонгацияДоговора", jsonText);  
context.OutputJson += "\n\n" + jsonText;
```

#### RabbitMQ Отправка комментария к объекту ERP
```
routineLabel = "ELMA.Документ.КомментарийКОбъекту1С";  
                var commentToErpSdelka = new CommentToERP () {  
                    ДатаОтправки = DateTime.Now,  
                    Ref = context.SDSdelka.Ref.Value,  
                    WorkflowInstance = context.WorkflowInstance.Uid,  
                    Text = context.NazvanieDogovora,  
                    Link = GetProcessUrl (context.WorkflowInstance)  
                };  
                jsonText = RbStatic.GetMcJsonFromAnObject (commentToErpSdelka, routineLabel: routineLabel, debugMode: false);  
                new RbSent ().SentToExchange ("From_Elma", routineLabel, jsonText);  
                context.OutputJson += "\n\n" + jsonText;
```

#### RabbitMQ Отправка сообщения в телегу
```
(new RbSent())).SentToQueue("Commutator", 
				@"{
				    ""Id"":""pdavydov@teko-com.ru"",
				    ""IdType"":""1c"",
				    ""MessageType"" : ""erp_info"",
				    ""Subject"" : ""test_erp"",
				    ""Text"" : ""test_erp""
				}"); 

			(new RbSent()).SentToQueue("Commutator",
				@"{
				    ""Id"":""5"",
				    ""IdType"":""elma"",
				    ""MessageType"" : ""elma_info"",
				    ""Subject"" : ""test_elma"",
				    ""Text"" : ""test_elma""
				}");
```