
## Razors

c:\ELMA3-Standart\Web\App_Data\CompiledModel\EleWise.ELMA.ConfigurationModel.dll
### Все разоры тут
- c:\ELMA3-Standart\UserConfig\WebApplication\Views\Shared\
- c:\ELMA3-Standart\Web\Modules\
- 
- *Вопросы* c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.BPM.Web.Tasks\Views\Question\
	
### Карточка Результата процесса * сводная информация
	c:\ELMA3-Standart\UserConfig\WebApplication\Views\Shared\Forms\Workflow\P_SoglasovatjDogovorKlient\ResultInfoPanel
	c:\ELMA3-Standart\UserConfig\WebApplication\Views\Shared\Forms\Workflow\P_SoglasovatjDogovorNashaF\ResultInfoPanel
	c:\ELMA3-Standart\UserConfig\WebApplication\Views\Shared\Forms\Workflow\P_CrServiceNoteChangeDetai\
	
	# Карточка Результата процесса * список контектсных перемнных
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\WorkflowInstance\InfoTabView.cshtml
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\WorkflowInstance\InfoTabViewEdit.cshtml

	
	# Карточка полоса сверху
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\WorkflowInstance\Info.cshtml /*Здесь вкладка по умолчанию*/
	
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\WorkflowInstance\Info.cshtml
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\WorkflowInstance\InfoTabView.cshtml
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Web\Views\Workflowtask\Execute.cshtml
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.BPM.Web.Common\Views\Shared\Warning.cshtml
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\WorkflowInstance\Info.cshtml
	
	# Карточка процесса серединка
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Processes.Web\Views\ProcessHeader\CommonInfo.cshtml
	C:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Web\Views\Shared\WorkflowInstance\CommonInfo.cshtml
	
	# карта процесса
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.Workflow.Web\Views\Shared\WorkflowInstance\Map.cshtml = 
	# Карточка пользователя 
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.BPM.Web.Security\Views\User\Views\ProfileCommon.cshtml
	# ЛОго 
	c:\ELMA3-Standart\Web\Content\Images\MenuImages\
	# Счетчик посещения страниц (общая страница)
	c:\ELMA3-Standart\Web\Themes\ELMATheme\Views\Layout.cshtml - завершение
	# Главная
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.BPM.Web.Content\Views\Page\Home.cshtml - 
	# Сотрудники
	c:\ELMA3-Standart\Web\Modules\EleWise.ELMA.EmployeeOfOrganization.Web\Views\Shared\Panels\
	
	# Календарь. список календаря(ListGrid) в календаре(Scheduler)
	C:\ELMA3-Standart\Web\Modules\EleWise.ELMA.BPM.Web.Calendar\Views\Scheduler\Controls\ListGrid.cshtml
	
	#Razor ErpUrl расположены в папке 
	C:\ELMA3-Standart\UserConfig\WebApplication\Views\Shared\Forms\Entities
	
	# Тековские стили 
	C:\ELMA3-Standart\Web\Content\Grid.css
	C:\ELMA3-Standart\Web\Content\Tables.css
}

{ /Логи PostgreSQL
	С:\Program Files\PostgreSQL\10\data\log\
	C:\Program Files\PostgreSQL\10\data\postgresql.conf
	log_statement = 'all'
}




{ /Новый год
	* Елочка. Замена картинки "Открыть главную страницу", она же Home
	C:\ELMA3-Standart\Web\Content\Images\MenuImages - здесь поменять название у нужных картинок. 
	Отображается картинка с названием  StartButtonSprite
	Отображается картинка с названием  StartButtonSpriteNewYear - сохраненная с елкой
	Отображается картинка с названием  StartButtonSprite23 - сохраненная 23 хаки
	Отображается картинка с названием  StartButtonSprite8 - сохраненная тюльпаном

	* Шапки на аватарках
	в C:\ELMA3-Standart\Web\Modules\EleWise.ELMA.BPM.Web.Security\Views\Shared\User\CurrentUserPanel.cshtml
	после
	<div id="UserInfo_Main">
		<div class="UserInfo_Photo">
			<a href="@Url.Action("CurrentProfile", "User", new { area = "EleWise.ELMA.BPM.Web.Security" })">
				@Html.UserPhotoWithoutSize(user)
	добавить
	<img style="margin-left:-60px;margin-top:0px" src="@(Url.Image("/Content/Images/head_h.png"))" />
	
	* можно попробовать ставить шарик на след год
	c:\ELMA3-Standart\Web\Content\Images\x32\Complete.png
}


{ /services
https://localhost:8081/api/v1/botx_elma_test/getall

http://localhost:8081/api/v1/botx_elma_test/getall


https://ws0429.teko-com.ru:8081/api/v1/botx_elma_test/getall
http://ws0429.teko-com.ru:8081/api/v1/botx_elma_test/getall




https://elma.teko-com.ru:8081/api/v1/botx_elma_test/getall
http://elma.teko-com.ru:8081/api/v1/botx_elma_test/getall

netsh http add urlacl url=https://+:8081/ user="local.teko-com.ru\pdavydov"
netsh http delete urlacl url=https://+:8081/



netsh http add sslcert ccs=443 appid={31568da0-22d3-4a44-adfa-3f9936b5278a}


netsh http add sslcert ipport=0.0.0.0:8081 certhash=dc817fa767cd05735f03b91c5c8c7f91f507e364 appid={31568da0-22d3-4a44-adfa-3f9936b5278a}

certutil -importPFX c:\Users\pdavydov\Downloads\ws0429.pfx

certutil -ImportCert "c:\Users\pdavydov\Downloads\iissert.cer"


c:\Users\pdavydov\Downloads\iis sert.cer
	
}

{/HELPERS
c:\ELMA3-Standart\Web\Themes\ELMATheme\Views\WF_Info.cshtml
}
