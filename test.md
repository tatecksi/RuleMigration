<table>
<thead>
<tr>
<th align="left">No.</th>
<th align="left">Type</th>
<th align="left">Sample Detection Rule</th>
<th align="left">Sample KQL</th>
<th align="left">Reference</th>
</tr>
</thead>
<tbody>
<tr>
<td>1.</td>
<td>**Filter** (and)</td>
<td><img src="media/caf5e11a5e0d7ed6dcc675c0caaaf7aa.png"> </td>
<td><br><pre>SecurityEventSecurityEvent<br>\| where EventID == 4728<br>\| where SubjectUserName =~ "AutoMatedService"<br>\| where isnotempty(SubjectDomainName)</pre><sub> *This assumes that the Windows Security Events are collected via MMA/AMA. Hence, we are using SecurityEvent table in Azure Sentinel.*<br><br>**Note:** <br> - Avoid case-insensitive operators (=~) when possible for query optimization. <br> - Use (==) if the value is not case-sensitive.<br> - Order the filters by starting with the 'where' statement that filter out the most data.</sub></td>
<td><sub> [String Operators](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/datatypes-string-operators#operators-on-strings)<br/>[Numerical Operators](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/numoperators)<br/>[ago](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/agofunction)<br/>[Datetime/timespan arithmetric](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/datetime-timespan-arithmetic)<br/>[between](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/betweenoperator)<br/>[now](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/nowfunction)<br/> [parse](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parseoperator)<br/>[extract](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/extractfunction)<br/>[parse_json](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parsejsonfunction)<br/>[parse_csv](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parsecsvfunction)<br/>[parse_path](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parsepathfunction)<br/>[parse_url](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parseurlfunction) </sub></td>
</tr>
<tr>
<td>2.</td>
<td>**Filter** (or) </td>
<td><img src="media/cf7c4ef2bf29e136988353de1355bec2.png"> </td>
<td>*<sub>Option 1: Use 'in'</sub>**<br><pre>SecurityEvent<br>\| where SubjectUserName in ("Adm1","ServiceAccount1","AutomationServices")</pre><br>**<sub>Option 2: Use 'or'</sub>**<br><pre>SecurityEvent<br>\| where SubjectUserName == "Adm1" or SubjectUserName == "ServiceAccount1" <br> or SubjectUserName == "AutomationServices"</pre></td>
<td><sub> [String Operators](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/datatypes-string-operators#operators-on-strings)<br/>[in](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/inoperator) </sub></td>
</tr>
</tbody>
</table>
