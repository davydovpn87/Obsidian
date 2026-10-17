

###  Поле ввода объекта располагается сразу за с его названием, а не через какой то промежуток
```
<table class = "show-lines table-form table-ColumnItem">
	<tr>
	    <td class="dft_table_td_inputs captionCell"   style="white-space:normal; text-align: right;   padding-right:5px;">
			@Html.Raw("Сервис") 
		</td>
		<td width="90%">
			@Html.Partial("EditorTemplates/Entity")
		</td>
	</tr>
</table>
```


###  Скопировано откуда то 

```
@using EleWise.ELMA.Web.Mvc.Views.ViewItem
@using System;
@using System.Linq;
@using System.Text;
@using EleWise.ELMA.Extensions;
@using EleWise.ELMA.Services;
@using EleWise.ELMA.Model.Managers;
@using EleWise.ELMA.Tasks;
@using EleWise.ELMA.Tasks.Components;
@using EleWise.ELMA.Tasks.Models;
@using EleWise.ELMA.Tasks.Managers;
@using System.Collections;
@using System.Collections.Generic;
@using EleWise.ELMA.UI.Controllers;
@using EleWise.ELMA.UI.Models;
@using EleWise.ELMA.BPM.Web.Tasks.Models;
@using EleWise.ELMA.BPM.Web.Common.Models;
@using EleWise.ELMA.BPM.Web.Tasks.Extensions;
@using EleWise.ELMA.BPM.Web.Common.Extensions;
@using EleWise.ELMA.Security.Models;
@using EleWise.ELMA.Web.Mvc.Html;
@using EleWise.ELMA.Model.Entities;
@using EleWise.ELMA.Model.Views;
@using EleWise.ELMA.Extensions;
@using EleWise.ELMA.Web.Mvc;
@using EleWise.ELMA.Model.Entities;
@using EleWise.ELMA.Model.Types.Settings;
@inherits ViewItemControl<PropertyViewItem, object, object>

<style>
.boards-container {
  display: grid;
  grid-auto-columns: 1fr;
  grid-auto-flow: column;
  grid-gap: 8px;
  /*height: 100vh;*/
  overflow: auto;
}

.boards-container__board {
    background: #EBECF0;
    border-radius: 3px;
    display: grid;
    grid-auto-rows: max-content;
    grid-gap: 10px;
    padding: 10px;
    min-width: 300px;
}

.boards-container__board h1 {
    font-size: 16px;
    margin: 0 0 10px 0;
}

.boards-container__board__card_1 {
    background: #FFF;
    box-shadow: 0 1px 0 rgba(9,30,66,.25);
    border-radius: 3px;
    padding: 10px;
    word-break: break-all;
}

.boards-container__board__card_2 {
    background: #FFF;
    box-shadow: 0 1px 0 rgba(9,30,66,.25);
    border-radius: 3px;
    padding: 10px;
    word-break: break-all;
    background-color: hsla(119, 100%, 81%, 0.3);
}

.boards-container__board__card_3 {
    background: #FFF;
    box-shadow: 0 1px 0 rgba(9,30,66,.25);
    border-radius: 3px;
    padding: 10px;
    word-break: break-all;
    background-color: hsla(33, 100%, 50%, 0.1);
    
}

.boards-container__board__card_4 {
    background: #FFF;
    box-shadow: 0 1px 0 rgba(9,30,66,.25);
    border-radius: 3px;
    padding: 10px;
    word-break: break-all;
    background-color: hsla(0, 100%, 30%, 0.1);
}


</style>

@{
	string logFile = "oit_razor";
	var kanbanViewModel = (dynamic)Model;
	var taskList = kanbanViewModel.GroupedTasks;
	var oitUsers = TekoStatic.GetUsersByOrgItem (85);
	var newExecFilter = string.Format("Id in ({0})", (oitUsers.Any () ? string.Join (",", oitUsers.Select (c => c.Id)) : "-1"));
	@functions {
		public User GetCurrentUser()
		{
			return EleWise.ELMA.Security.Services.AuthenticationService.GetCurrentUser<EleWise.ELMA.Security.Models.IUser>() as User;
		}
	}
}

@helper DrawKanban(dynamic groupedTasks, bool showClosed)
{
	try
	{
		string url = EleWise.ELMA.Services.Locator.GetServiceNotNull<EleWise.ELMA.Runtime.Settings.CommonSettingsModule> ().Settings.ApplicationBaseUrl + "/UI/Page/RequestForTS";
		<div class="boards-container">
			@foreach(var groupedTask in groupedTasks)
			{
				Guid oitGuid = Guid.Parse("19a65018-4275-4708-a436-b4fd1473f918");
				List<long> worfklowInstanceIds = new List<long>();
				<div class="boards-container__board">
					<h2>@Html.Raw(groupedTask.Key)</h2>
					@foreach(var row in groupedTask.Value)
					{
						var currentTasks = string.Join("<br/>",(string[])row.Tasks);
						<div class="boards-container__board__card_@row.WorkflowStatus">
							
							<div title="@row.Opisanie"><a href="@row.Url">@row.ProcessName</a> </div>
							
							<br/>
							
					    		<div style=" dislpay: @(showClosed ? "none" : "");">@Html.Raw(@currentTasks)</div>
					    		
					    		@if (row.Epik != long.MaxValue) 
			    					{
			    						<font size="-2" style="background-color:hsla(164, 100%, 100%, 0.2);">@Html.Raw(row.EpikName)</font>
			    					}
			    					
			    					
					    		
						    	<div style="text-align: right; float: right; padding-top:3px;">
					    			@*<font size="2" style="background-color:hsla(63, 100%, 63%, 0.5);">
					    				@Html.Raw(string.Format("<b>{0} - {1}</b>", string.IsNullOrWhiteSpace(row.Priority) ? "*" : row.Priority, string.IsNullOrWhiteSpace(row.Difficulty) ? "*" : row.Difficulty))
					    			</font>*@
					    			
					    			@if (!string.IsNullOrWhiteSpace(row.StadiyaToString)) 
			    					{
			    						<font size="-2" style="background-color:hsla(173, 100%, 100%, 0.5); font-weight: bold;">@Html.Raw(row.StadiyaToString)</font>
			    					}
					    			
						    	</div>
						</div>
					}
				</div>
			}
		</div>
	}
	catch(Exception e)
	{
		<h1>@e.ToString()</h1>
	}
}

@DrawKanban(taskList, kanbanViewModel.ShowClosed)

@Html.PopupWindow("OIT_TaskPopUp", SR.T("Задача"), @<text></text>, width: 1600)
@Html.PopupWindow("TestPopUp", SR.T("Test"), @<text></text>, width: 1600)
@Html.PopupWindow("ReAssignSelectedTask", SR.T("Изменение исполнителя задачи"), @<text></text>,width: 600)
<script type="text/javascript" charset="windows-1251">
	$(document).ready(function () {
		var p1 = document.getElementById('Context_Button-58d9b20c-d2de-486d-94d0-a5b40911b1e3');
		if (p1)
		{
			p1.style.display = "none";
		}
		var p2 = document.getElementById('Context_Button-4a09edbe-742d-499e-a25b-db77d38cfd96');
		if (p2)
		{
			p2.style.display = "none";
		}
     });

	
	function CloseInstanceTimer(sender) {
        if (confirm('@(SR.T("Прервать таймер и продолжить исполнение экземпляра процесса?"))')) {
            elma.openPleaseWait(true);
            $.ajax({
                url: '@Url.Action("CloseTimer", "WorkflowInstance", new { area = EleWise.ELMA.Workflow.Processes.Web.ProcessesRouteProvider.AreaName })?schedulerJobId=' + sender.id,
                type: 'POST',
                success: function (data, status, xhr) {
                    elma.closePleaseWait(true);
                    setTimeout(function () {
                        document.getElementById('Context_Button-ba7730cc-937b-4132-a39d-4b7bf2ffafe3').click();
                    }, 500);
                }
            });
        }
    }
    
	function TestFunc(sender) 
	{
		var combobox = $('#Context_NewExecutor_Id').data("tComboBox");
		var question = 'Прервать таймер и передать экземпляра процесса на исполнение: ' + combobox.text() + '?';
		if (confirm(question))
        //if (confirm('@(SR.T("Прервать таймер и продолжить исполнение экземпляра процесса?"))')) 
        {
  			$('#Context_SelectedWorkflowInstance_Id').data("tComboBox").reloadSelect(sender.id);
  			
			setTimeout(() => {  document.getElementById('Context_Button-ba7730cc-937b-4132-a39d-4b7bf2ffafe3').click(); }, 1000);
        }
    }
    
	function ShowSelectedTask(sender, taskId, objectGuid) 
	{
        var popupId = 'OIT_TaskPopUp';
        var urlObjectPopup = '@(Html.Url().Action("ViewItem", "Catalogs", new { area = EleWise.ELMA.BPM.Web.Common.CommonAreaRegistration.AREA_NAME}))';
		var url = urlObjectPopup +
            '/' + taskId +
            '?uid=' + objectGuid + 
            '&popupId=' + popupId + '&inPopUp=True';
        refreshPopup(popupId, url, null,'@(SR.T("ОИТ Задача"))');
    }
    
    @*function TestFunc(sender) 
	{
        var popupId = 'TestPopUp';
        var urlObjectPopup = '@(Html.Url().Action("Page", "UI", new { area = SDKAreaRegistration.AREA_NAME, pageId = 50, name = "welcome"}))';
        var url = urlObjectPopup;
        refreshPopup(popupId, url, null,'@(SR.T("Изменение согласующего"))');
    }*@
    
    function TestReassignTB(sender, taskId)
    {
    	var urlObjectPopup = '@(Html.Url().Action("ReAssignTaskForm", "Task", new { area = EleWise.ELMA.BPM.Web.Tasks.RouteProvider.AreaName }))';
         var url = urlObjectPopup +
            '?taskId=' + taskId + '&popupId=ReAssignSelectedTask&inPopUp=True&RequiredStartDate=False&RequiredEndDate=False&HideStartDate=False&HideEndDate=False&NeedCheckOrganizationItem=True&HideReAssignTaskPriority=False&IsAjax=True';
    	refreshPopup('ReAssignSelectedTask', url, null,'Смена исполнителя текущей задачи');
    }
    
	$(document).ajaxSuccess(
	  	function(event, xhr, setting)
	  	{   
			if (event && setting && event.type === 'ajaxSuccess')
  			{
			    const headers = document.querySelectorAll('.boards-container__board h1');
			    if(!headers) return;
			    let maxHeight = 0;
			    headers.forEach(header => {
			    	maxHeight = Math.max(maxHeight, header.clientHeight)
			    })
			    
			   	headers.forEach(header => {
			   		if(maxHeight < 70)
			   		{
			   			header.style.height = `${maxHeight}px`;
			   		}
			    });
  			}	
	  	}
	);
</script>
```