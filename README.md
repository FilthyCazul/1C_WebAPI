# 1C_WebAPI
Дипломная работа ученика группы "КС-18-01", Вдовенко Дмитрия Александровича. 
Разделы API: Volume 1 (GET-запросы на информацию, без параметров или с малым кол-вом параметров, передаваемых в строке URL), Volume 2 (POST-запросы, требующие заполнения параметров, передающихся в теле запроса).
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Пояснения:
	
	АдресРесурса 	- адрес, по которому имеется доступ к серверу, на котором развёрнута база и расширение.
	АдресБазы 		- адрес, по которому имеется доступ к базе, чаще всего в роли этого адреса при публикации выступает наименование базы.
	ДатаНачала 		- дата начала отбора.
	ДатаОкончания 	- дата окончания отбора.
	ТипДокумента	- тип/наименование документа, использующееся как указание типов документов для отбора.
	ГУИД 			- Глобальный уникальный идентификатор, использующийся в методах с точечной выборкой информации.
	ПолеИсключение* - поле, которое будет исключено из структуры выборки документа.
	НомерДокумента* - номер документа, участвующего в выборке.
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
VOLUME 1 //GET-запросы

1. Ping - метод, по которому можно проверить работоспособность API и корретность настроек публикации базы данных с расширением. Возвращает ответ с текстом "ОК" и статусом 200.

URL 	= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/ping
Example	= http://127.0.0.1/UNFDemo/hs/arrdif/ping

2. V1_objbyguid - метод, возвращающий полное описание ссылочного объекта из БД, опознавание объекта предусмотрено через его уникальный идентификатор.
Описание возвращается в формате структур и массивов JSON, содержащих в себе список свойств объекта и их значения. В целях гибкости все значения приведены к строковому типу.
	
Параметры URL: guid.

URL		= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/objbyguid?[ГУИД] |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v1/objbyguid?guid=f15147db-3a2e-11e8-8781-b06ebf31a92f

3. V1_docnames - служебный метод, возвращающий список наименований типов документов. Необходим для того, чтобы в дальнейшем получать данные документов по этим типам.
Служит своеобразной памяткой для получения дальнейших данных, т.к. перечень документов в 1С довольно большой.

URL		= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/docnames |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v1/docnames

4. V1_nomenclaturelist - метод, возвращающий список структур с наименованиями, артикулами и GUID'ами номенклатуры.

URL 	= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/nomenclaturelist |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v1/nomenclaturelist

5. V1_storagelist - метод, возвращающий список складов с их уникальными идентификаторами.

URL 	= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/storagelist |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v1/storagelist

6. V1_doclist - метод, возвращающий список краткой информации о документах заданного типа.
Содержит такие свойства объектов, как: ГУИД, Дата, Номер, Организация, Контрагент, Сумма. Если значение структуры не заполнено, то элемент структуры автоматически очищается.
Имеет параметризацию: 	1) doctype - имя/тип документа, который можно выбрать из списка метода "V1_docnames". Параметр является обязательным.
						2) datetime_start & datetime_end - дата начала и окончания отбора выводимых в список документов. Параметры не являются обязательными, но работают только в связке. 
						В случае, когда нужно получить документы за определенный период (например, с начала существовании базы по конец прошлого квартала) в начальную дату стоит передать значение "01.01.1980 00:00:00".
						Дата задаётся только в определённом шаблоне, ГГГГММДДЧЧММСС. Например, значение "29 апреля 2023 г., 18 часов 41 минута и 25 секунд" будет выглядеть как "20230429184125"

URL		= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/doclist?doctype=[ТипДокумента] ИЛИ http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/doclist?doctype=АктВыполненныхРабот&datetime_start=[ДатаНачала]&datetime_end=[ДатаОкончания] |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v1/doclist?doctype=АктВыполненныхРабот ИЛИ http://127.0.0.1/UNFDemo/hs/arrdif/v1/doclist?doctype=АктВыполненныхРабот&datetime_start=19800101000000&datetime_end=20230429184125 

7. V1_getconnections - метод, возвращающий информацию о связанных документах. В отличии от представления в "1С:Предприятии", показывает полный стек связанных документов.
Передаёт все данные в структуре дерева, используя вложенные строки. Все ссылочные значения, получаемые этим методом, дополняются уникальными идентификаторами. Например, за полем с ключом "Ссылка" сразу последует поле с ключом "Ссылка_GUID".
Имеет параметризацию:	1) guid - уникальный идентификатор документа, чью цепочку нужно отследить. Параметр является обязательным.

URL 	= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v1/getconnections?guid=[ГУИД] |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v1/getconnections?guid=f15147db-3a2e-11e8-8781-b06ebf31a92f
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
VOLUME 2 //POST-запросы

1. V2_getdocbyinfo - метод, возвращающий полную информацию о документах по указанному типу. Помимо основной информации, так же содержит преобразованные табличные части документов.
Имеет параметризацию: 	1)doctype - имя/тип документа, который можно выбрать из списка метода "V1_docnames". Параметр является обязательным.
						2)docnum - номер или массив номеров документов, по которым осуществляется отбор. Параметр является обязательным.
						3) datetime_start & datetime_end - дата начала и окончания отбора выводимых в список документов. Параметры не являются обязательными, но работают только в связке. 
						В случае, когда нужно получить документы за определенный период (например, с начала существовании базы по конец прошлого квартала) в начальную дату стоит передать значение "01.01.1980 00:00:00".
						Дата задаётся только в определённом шаблоне, ГГГГММДДЧЧММСС. Например, значение "29 апреля 2023 г., 18 часов 41 минута и 25 секунд" будет выглядеть как "20230429184125".
						4)exept - проанализировав структуру документа, в этот параметр можно задать массив тех реквизитов, которые требуется исключить из выгрузки. Не работает на вложенные поля табличных частей. Параметр не является обязательным. !Строгая типизация - массив!
						
						!ПАРАМЕТРЫ ПЕРЕДАЮТСЯ В ТЕЛЕ ЗАПРОСА!
						
				Шаблон:	{														Пример:	{	
							"doctype": "*ТипДокумента*",									"doctype": "ЗаказПокупателя",					//Параметр должен задаваться строго строковым типом
							"docnum": [*НомерДокумента1*, *НомерДокумента2*],				"docnum": ["АСФР-000043", "АСФР-000041"],		//Параметр должен задаваться строго строковым типом
							"exept": [*ПолеИсключение1*, *ПолеИсключение2*...] 				"exept": ["Калькуляция", "ПараметрыДоставки"]	//Параметр должен задаваться строго строковым типом, необязательный
						}	
						
URL		= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v2/getinfospecial |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v2/getinfospecial

2. V2_getstoragecontent - метод, возвращающий информацию по остаткам продукции на складах.
Показывает остатки на текущий момент времени.
Имеет параметризацию: 	1) storage_byguid - булево, отражающее признак того, уточняются ли склады по ГУИД'у или по наименованию. Истина = ГУИД; Ложь = Наименование. Параметр является обязательным.
						2) storagelist - массив данных по уточнению. В зависимости от значения первого параметра содержит либо массив ГУИД'ов, либо массив наименований складов, остатки по которым нужно просмотреть. Параметр является обязательным. !Строгая типизация - массив!
						3) specifyproduct - уточнение по конктретной номенклатуре. Сам факт наличия этого параметра включает уточнение по номенклатуре, значение этого параметра, по аналогии с первым параметром, является флагом для следующего параметра. 
						Может быть задано три значения: "art" - поиск номенклатуры по артикулу, "guid" - поиск по уникальному идентификатору, "name" - поиск по наименованию. Параметр не является обязательным, работает только в связке с параметром №4. !Строгая типизация - одно из трёх заданных значений!
						4) productlist - массив данных-уточнений номенклатуры. В зависимости от параметра №3, должен содержать либо гуид, либо артикул, либо точное наименование номенклатуры. Параметр не является обязательным, работает только в связке с параметром №3. !Строгая типизация - массив!
						
						!ПАРАМЕТРЫ ПЕРЕДАЮТСЯ В ТЕЛЕ ЗАПРОСА!
						
				Шаблон:	{															Пример:	{	
							"storage_byguid": *булево*,											"storage_byguid": false,											//Параметр должен задаваться строго типом булево
 							"storagelist": [*Значение1*, *Значение2*...],						"storagelist": ["Производство (цех)", "Маркетинг и продажи"],		//Параметр должен задаваться строго строковым типом
							"specifyproduct": *флаг* 											"specifyproduct": "art",											//Параметр должен задаваться строго строковым типом, необязательный, одно из трех значений ("art", "guid", "name"), работает только в связке с п.№4
							"productlist": [*Значение1*, *Значение2"...]						"productlist": ["3612"]												//Параметр должен задаваться строго строковым типом, необязательный, значение зависит от п. №3, работает только в связке с п.№3
						}																	}
						
URL 	= http://[АдресРесурса]/[АдресБазы]/hs/arrdif/v2/getstoragecontent |
Example = http://127.0.0.1/UNFDemo/hs/arrdif/v2/getstoragecontent
