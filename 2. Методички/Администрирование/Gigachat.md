
❔GigaChat 
Текст запроса - isdeleted + наименование
pbx_events user
pbx_events date, from, call id
pbx_call user date internal, идея, need_b24_sync
таблица звонков битрикс

https://elma.teko-com.ru/Common/Catalogs/ViewItem/1371?uid=a23cd995-4ed9-413b-a169-9f5347b5bfcc 

Дописать в название о необрабатываемости исключения



select 'GigaChat.Doc.Request', json_build_object(
        'docId',doc.id,
        'fileUid',file,
        'fileName',ff."Name",
        'contentType', ff.contenttype,
        'gigaFileUid', doc.gigachatuid,
        'promptId',tg_req.id,
        'prompt',tg_req.zapros)
from teko_gigachatdoc doc
    left join teko_gigachat_response tg_res on doc.id = tg_res.gigadoc
    left join teko_gigachat_request tg_req on tg_req.id = 2 -- teko_gigachat_request Только Json
    left join fs_files ff on ff.uid = file::uuid
where tg_res.id is null  and ff.contenttype = 'application/pdf'
and doc.id not in (1131, 1123, 1122, 1106, 1105, 1098)
order by doc.id desc 
limit 10;



поле unsupported, process? dokument 
добавить в service Teko_GigaChatDoc


public partial class GigachatRequest
  {
    [JsonProperty("docId")]
    public long DocId { get; set; }

    [JsonProperty("fileUid")]
    public Guid FileUid { get; set; }

    [JsonProperty("fileName")]
    public string FileName { get; set; }

    [JsonProperty("contentType")]
    public string ContentType { get; set; }

    [JsonProperty("promptId")]
    public long PromptId { get; set; }

    [JsonProperty("prompt")]
    public string Prompt { get; set; }
  }