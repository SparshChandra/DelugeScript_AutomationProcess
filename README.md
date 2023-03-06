# DelugeScript_AutomationProcess
This code retrieves attachments associated with a specific record in Zoho CRM, converts the data contained within the attachments into lead records, and returns a list of the IDs of the newly created lead records.

Created a Custom Button field in the property database module for attachemnt extraction in json format, to push into CRM

<img width="1140" alt="image" src="https://user-images.githubusercontent.com/102770866/223236317-fc5325f6-83db-47c6-b5de-0a0cf6cecd7e.png">
<img width="1421" alt="image" src="https://user-images.githubusercontent.com/102770866/223236412-7ec9e37f-4e0d-4dd0-8238-c89b80c573ff.png">

Created a Custom Function, for fetching the Property Database ID

<img width="1425" alt="image" src="https://user-images.githubusercontent.com/102770866/223236555-ad8b2931-79e1-4d4d-a2e5-002a2850476a.png">


Deluge script explaination

get_attachments = zoho.crm.getRelatedRecords("Attachments","PropertyDatabase",get_id);: This line retrieves all the attachments related to a specific record in the "PropertyDatabase" module. The get_id variable should contain the ID of the record.

if(get_attachments.size() > 0): This line checks whether there are any attachments returned by the previous line.

for each r in get_attachments: This line starts a loop that iterates over each attachment.

get_file = invokeurl [...]: This line retrieves the actual file associated with the current attachment by making a HTTP GET request to the Zoho CRM API. The get_id and r.getJSON("id") variables are used to construct the URL of the API endpoint. The resulting file content is stored in a map called dummy_map.

replace_value = if(dummy_map.get("Dummy_Key").contains("\r\n"),"\r\n","\n"): This line replaces any occurrences of Windows-style line endings (\r\n) in the file content with Unix-style line endings (\n), and stores the result in replace_value.

convert_into_list = "[" + dummy_map.get("Dummy_Key").replaceAll(replace_value,"],[") + "]": This line converts the file content from a string of comma-separated values into a list of lists. Each sublist represents a row of data, with each item in the sublist representing a column. The resulting list of lists is stored in convert_into_list.

<img width="1413" alt="image" src="https://user-images.githubusercontent.com/102770866/223236661-6b1b1047-29f6-4c1d-997f-6679acef1ace.png">


for each r in convert_into_list.toList(): This line starts a loop that iterates over each row in convert_into_list.

if(x > 1): This line skips the first row of convert_into_list, which is assumed to contain column headings rather than data.

if(r.get(130) != "" && r.get(128) != "" && r.get(132) != "" && r.get(133) != ""): This line checks whether the current row contains valid data. If all four columns (130, 128, 132, and 133) are not empty, the code proceeds to create a new lead record in Zoho CRM.

insert_map = Map(): This line creates a new map called insert_map, which will be used to store the data for the new lead record.

insert_map.put(...): This line populates the insert_map map with data from the current row of convert_into_list. Each column in the row is mapped to a specific field in the Leads module of Zoho CRM.

resp_lead = zoho.crm.createRecord("Leads",insert_map): This line creates a new lead record in Zoho CRM using the data in insert_map. The resulting lead record is stored in resp_lead.

id_list.add(resp_lead.getJSON("id")): This line adds the ID of the newly created lead record to a list called id_list.

return id_list: This line returns the id_list to the calling function.

<img width="1417" alt="image" src="https://user-images.githubusercontent.com/102770866/223236776-db17fbfa-5462-4067-9015-9c6f676201be.png">

<img width="1292" alt="image" src="https://user-images.githubusercontent.com/102770866/223236906-b12e7ae9-c265-4cea-a541-bca5cf0a95f7.png">
