
<h2><u>Common Search Commands</u></h2>
<br />

<sub>**SPL Command** | <sub>**Description** | <sub>**SPL Example** | <sub>**KQL** | <sub>**KQL Example** | 
--- | --- | --- | --- | --- | 
<sub>chart/ timechart | <sub>Returns results in a tabular output for (time-series) charting. | |<sub>[render](https://docs.microsoft.com/azure/data-explorer/kusto/query/renderoperator?pivots=azuredataexplorer) |<sub><pre> … \| render timechart</pre> 
<sub>dedup | <sub>Removes subsequent results that match a specified criterion. | |<sub>[distinct](https://docs.microsoft.com/azure/data-explorer/kusto/query/distinctoperator) <br /> [summarize](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator)<sub/>  | <sub><pre>… \| summarize by Computer, EventID </pre>	
eval | <sub>Calculates an expression.  See COMMON EVAL FUNCTIONS. | | <sub>[extend](https://docs.microsoft.com/azure/data-explorer/kusto/query/extendoperator) | <sub><pre>T \| extend duration = endTime - startTime</pre> 
fields | <sub>Removes fields from search results. | |<sub> [project](https://docs.microsoft.com/azure/data-explorer/kusto/query/projectoperator) <br /> [project-away](https://docs.microsoft.com/azure/data-explorer/kusto/query/projectawayoperator) |<sub> <pre>T \| project cost=price*quantity, price</pre>	
head/tail |<sub> Returns the first/last N results. |  | <sub>[top](https://docs.microsoft.com/azure/data-explorer/kusto/query/topoperator) |<sub> <pre> T \| top 5 by Name desc nulls last </pre>
lookup | Adds field values from an external source. | | [externaldata](https://docs.microsoft.com/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuredataexplorer) <br /> [lookup](https://docs.microsoft.com/azure/data-explorer/kusto/query/lookupoperator) | <pre> Users <br /> \| where UserID in ((externaldata (UserID:string) [<br /> @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" <br /> h@"?...SAS..." // Secret token needed to access the blob <br /> ])) \| ... </pre>
rename | Renames a field. Use wildcards to specify multiple fields. | | [project-rename](https://docs.microsoft.com/azure/data-explorer/kusto/query/projectrenameoperator) | <pre> T \| project-rename new_column_name = column_name </pre>
rex | Specifies regular expression named groups to extract fields. | | [matches regex](https://docs.microsoft.com/azure/data-explorer/kusto/query/re2) | <pre>… \| where field matches regex "^addr.*" </pre>
search | Filters results to those that match the search expression. | | [search](https://docs.microsoft.com/azure/data-explorer/kusto/query/searchoperator?pivots=azuredataexplorer) | <pre>search "X"</pre>
sort | Sorts the search results by the specified fields. | | [sort](https://docs.microsoft.com/azure/data-explorer/kusto/query/sortoperator) | <pre> T \| sort by strlen(country) asc, price desc </pre>
stats | Provides statistics, grouped optionally by fields. | | [summarize](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator) | <pre> Sales \| summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime) </pre>
mstats | Provides statistics, grouped optionally by fields. | | [summarize](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator) | <pre> T \| summarize count() by price_range=bin(price, 10.0) </pre>
table | Specifies fields to keep in the result set. Retains data in tabular format. | | [project](https://docs.microsoft.com/azure/data-explorer/kusto/query/projectoperator) | <pre>T \| project columnA, columnB</pre>
top/rare | Displays the most/least common values of a field. | | [top](https://docs.microsoft.com/azure/data-explorer/kusto/query/topoperator) | <pre>T \| top 5 by Name desc nulls last </pre>
transaction | Groups search results into transactions. | sourcetype=MyLogTable type=Event<br />\| transaction ActivityId startswith="Start" endswith="Stop"<br />\| Rename timestamp as StartTime<br />\| Table  City, ActivityId, StartTime, Duration | Refer to example | <pre>let Events = MyLogTable \| where type=="Event";<br />Events<br />\| where Name == "Start"<br />\| project Name, City, ActivityId, StartTime=timestamp<br />\| join (Events<br />\| where Name == "Stop"<br />\| project StopTime=timestamp, ActivityId)<br />on ActivityId<br />\| project City, ActivityId, StartTime, Duration = StopTime – StartTime</pre>
eventstats | Generates summary statistics from fields in your events and saves those statistics in a new field. | … \| bin span=1m _time<br />\|stats count AS count_i by _time, category <br />\| eventstats sum(count_i) as count_total by _time | Refer to example | <pre>let binSize = 1h;<br />let detail = SecurityEvent <br />\| summarize detail_count = count() by EventID, tbin = bin(TimeGenerated, binSize);<br />let summary = SecurityEvent<br />\| summarize sum_count = count() by tbin = bin(TimeGenerated, binSize);<br />detail <br />\| join kind=leftouter (summary) on tbin <br />\| project-away tbin1</pre> <br /> Or <br /><br /> <pre>let binSize = 1m;<br />SecurityEvent<br />\| where TimeGenerated >= ago(24h)<br />\| summarize TotalEvents = count() by EventID, groupBin =bin(TimeGenerated, binSize)<br />\|summarize make_list(EventID), make_list(TotalEvents), sum(TotalEvents) by groupBin<br />\| mvexpand list_EventID, list_TotalEvents</pre>
streamstats | Find the cumulative sum | ... \| streamstats sum(bytes) as bytes _ total \| timechart | |
anomalydetection | Find anomalies in the field | sourcetype=nasdaq earliest=-10y <br />\| anomalydetection Close _ Price | [series_decompose_anomalies()](https://docs.microsoft.com/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction) | <pre>let LookBackPeriod= 7d;<br />let disableAccountLogon=SignIn<br />\| where ResultType == "50057"<br />\| where ResultDescription =~ "User account is disabled. The account has been disabled by an administrator.";<br />disableAccountLogon<br />\| make-series Trend=count() default=0 on TimeGenerated in range(startofday(ago(LookBackPeriod)), now(), 1d)<br />\| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(Trend)<br />\| extend (anomalies,score) = series_decompose_anomalies(Trend)</pre>
where | Filters search results using eval expressions. Used to  compare two different fields. | | [where](https://docs.microsoft.com/azure/data-explorer/kusto/query/whereoperator) | <pre>T \| where fruit=="apple"</pre>
 |  | | | | 
 
<br />
<br />
<br />
<br />

<h2><u>Common Eval Commands</u></h2>
<br />

SPL Command | Description | SPL Example | KQL | KQL Example | 
--- | --- | --- | --- | --- | 
abs(X) | Returns the absolute value of X. | abs(number) | [abs()](https://docs.microsoft.com/azure/data-explorer/kusto/query/abs-function) | <pre>abs(X)</pre>
case(X,"Y",…) | Takes pairs of arguments X and Y, where X arguments are Boolean expressions. When evaluated to TRUE, the arguments return the corresponding Y argument. | case(error == 404, "Not found", error == 500,"Internal Server Error", error == 200, "OK") | [case](https://docs.microsoft.com/azure/data-explorer/kusto/query/casefunction) | <pre>T<br />\| extend Message = case(error == 404, "Not found", error == 500,"Internal Server Error", "OK") </pre>
ceil(X) | Ceiling of a number X. | ceil(1.9) | [ceiling()](https://docs.microsoft.com/azure/data-explorer/kusto/query/ceilingfunction) | <pre>ceiling(1.9)</pre>
cidrmatch("X",Y) | Identifies IP addresses that belong to a particular subnet.  | cidrmatch("123.132.32.0/25",ip) | [ipv4_is_match()](https://docs.microsoft.com/azure/data-explorer/kusto/query/ipv4-is-matchfunction)<br />[ipv6_is_match()](https://docs.microsoft.com/azure/data-explorer/kusto/query/ipv6-is-matchfunction) | <pre>ipv4_is_match('192.168.1.1', '192.168.1.255') == false</pre>
coalesce(X,…) | Returns the first value that is not null. | coalesce(null(), "Returned val", null()) | [coalesce()](https://docs.microsoft.com/azure/data-explorer/kusto/query/coalescefunction) | <pre>coalesce(tolong("not a number"), tolong("42"), 33) == 42</pre>
cos(X) | Calculates the cosine of X. | n=cos(0) | [cos()](https://docs.microsoft.com/azure/data-explorer/kusto/query/cosfunction) | <pre>cos(X)</pre>
exact(X) | Evaluates an expression X using double precision floating point arithmetic. | exact(3.14*num) | [todecimal()](https://docs.microsoft.com/azure/data-explorer/kusto/query/todecimalfunction) | <pre>todecimal(3.14*2)</pre>
exp(X) | Returns eX. | exp(3) | [exp()](https://docs.microsoft.com/azure/data-explorer/kusto/query/exp-function) | <pre>exp(3)</pre>
if(X,Y,Z) | If X evaluates to TRUE, the result is the second argument Y. If X evaluates to FALSE, the result evaluates to the third argument Z. | if(error==200, "OK", "Error") | [iif()](https://docs.microsoft.com/azure/data-explorer/kusto/query/iiffunction) | <pre>iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")</pre>
isbool(X) | Returns TRUE if X is Boolean. | isbool(field) | [iif()](https://docs.microsoft.com/azure/data-explorer/kusto/query/iiffunction) <br /> [gettype()](https://docs.microsoft.com/azure/data-explorer/kusto/query/gettypefunction) | <pre>iif(gettype(X) =="bool","TRUE","FALSE")</pre>
isint(X) | Returns TRUE if X is Integer. | isint(field) | [iif()](https://docs.microsoft.com/azure/data-explorer/kusto/query/iiffunction) <br /> [gettype()](https://docs.microsoft.com/azure/data-explorer/kusto/query/gettypefunction) | <pre>iif(gettype(X) =="long","TRUE","FALSE")</pre>
isnull(X) | Returns TRUE if X is NULL. | isnull(field) | [isnull()](https://docs.microsoft.com/azure/data-explorer/kusto/query/isnullfunction) | <pre>isnull(field)</pre>
isstr(X) | Returns TRUE if X is String. | isstr(field) | [iif()](https://docs.microsoft.com/azure/data-explorer/kusto/query/iiffunction) <br /> [gettype()](https://docs.microsoft.com/azure/data-explorer/kusto/query/gettypefunction) | <pre>iif(gettype(X) =="string","TRUE","FALSE")</pre>
len(X) | This function returns the character length of a string X. | len(field) | [strlen()](https://docs.microsoft.com/azure/data-explorer/kusto/query/strlenfunction) | <pre>strlen(field)</pre>
like(X,"y") | Returns TRUE if and only if X is like the SQLite pattern in Y. | like(field, "addr%") | [has](https://docs.microsoft.com/azure/data-explorer/kusto/query/has-anyoperator) <br /> [contains](https://docs.microsoft.com/azure/data-explorer/kusto/query/datatypes-string-operators) <br /> [startswith](https://docs.microsoft.com/azure/data-explorer/kusto/query/datatypes-string-operators) <br /> [matches regex](https://docs.microsoft.com/azure/data-explorer/kusto/query/re2) | <pre>… \| where field has "addr"</pre><br /><pre>… \| where field contains "addr"</pre><br /><pre>… \| where field startswith "addr"</pre><br /><pre>… \| where field matches regex "^addr.*"</pre>
log(X,Y) | Returns the log of the first argument X using the second argument Y as the base. Y defaults to 10. | log(number,2) | [log](https://docs.microsoft.com/azure/data-explorer/kusto/query/log-function) <br /> [log2](https://docs.microsoft.com/azure/data-explorer/kusto/query/log2-function) <br /> [log10](https://docs.microsoft.com/azure/data-explorer/kusto/query/log10-function) | <pre>log(X)</pre><br /><pre>log2(X)</pre><br /><pre>log10(X)</pre>
lower(X) | Returns the lowercase of X. | lower(username) | [tolower](https://docs.microsoft.com/azure/data-explorer/kusto/query/tolowerfunction) | <pre>tolower(username)</pre>
ltrim(X,Y) | Returns X with the characters in Y trimmed from the left side. Y defaults to spaces and tabs. | ltrim(" ZZZabcZZ ", " Z") | [trim_start()](https://docs.microsoft.com/azure/data-explorer/kusto/query/trimstartfunction) | <pre>trim_start(“ ZZZabcZZ”,” ZZZ”)</pre>
match(X,Y) | Returns if X matches the regex pattern Y. | match(field, "^\d{1,3}\.\d$") | [matches regex](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/re2) | <pre>… \| where field matches regex @"^\d{1,3}\.\d$")</pre>
max(X,…) | Returns the maximum. | max(delay, mydelay) | [max()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/max-aggfunction) <br /> [arg_max()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/arg-max-aggfunction) | <pre>… \| summarize max(field)</pre>
md5(X) | Returns the MD5 hash of a string value X. | md5(field) | [hash_md5](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/md5hashfunction) | <pre>hash_md5("X")</pre>
min(X,…) | Returns the minimum. | min(delay, mydelay) | [min_of()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/min-offunction)<br /> [min()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/min-aggfunction)<br /> [arg_min](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/arg-min-aggfunction) | <pre>min_of (expr_1, expr_2 ...)</pre><br /><pre>…\|summarize min(expr)</pre><br /><pre>…\| summarize arg_min(Price,*) by Product</pre>
mvcount(X) | Returns the number of values of X. | mvcount(multifield) | [dcount](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/dcount-aggfunction) | <pre>…\| summarize dcount(X) by Y</pre>
mvfilter(X) | Filters a multi-valued field based on the Boolean expression X. | mvfilter(match(email, "net$")) | | 
mvindex(X,Y,Z) | Returns a subset of the multivalued field X from start position (zero-based) Y to Z (optional). | mvindex( multifield, 2) | |
mvjoin(X,Y) | Given a multi-valued field X and string delimiter Y, and joins the individual values of X using Y. | mvjoin(address, ";") | |
now() | Returns the current time, represented in Unix time. | now() | [now()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/nowfunction) |<pre>now()</pre><br /><pre>now(-2d)</pre>
null() | This function takes no arguments and returns NULL. | null() | [null](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/scalar-data-types/null-values?pivots=azuredataexplorer) | <pre>null</pre>
nullif(X,Y) | Given two arguments, fields X and Y, and returns the X if the arguments are different. Otherwise returns NULL. | nullif(fieldA, fieldB) | [iif](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/iiffunction) | <pre>iif(fieldA==fieldB, null, fieldA)</pre>
random() | Returns a pseudo-random number ranging from 0 to 2147483647. | random() | [rand()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/randfunction) | <pre>rand()</pre>
relative_ time(X,Y) | Given epochtime time X and relative time specifier Y, returns the epochtime value of Y applied to X. | relative_time(now(),"-1d@d") | [unix time](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/datetime-timespan-arithmetic#example-unix-time) | <pre>let toUnixTime = (dt:datetime)<br />{<br />(dt - datetime(1970-01-01))/1s <br />};</pre>
replace(X,Y,Z) | Returns a string formed by substituting string Z for every occurrence of regex string Y in string X. | Returns date with the month and day numbers switched, so if the input was 4/30/2015 the return value would be 30/4/2009: replace(date, "^(\d{1,2})/ (\d{1,2})/", "\2/\1/") | [replace()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/replacefunction) | <pre>replace( @'^(\d{1,2})/(\d{1,2})/', @'\2/\1/',date)</pre>
round(X,Y) | Returns X rounded to the amount of decimal places specified by Y. The default is to round to an integer. | round(3.5) | [round()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/roundfunction) | <pre>round(3.5)</pre>
rtrim(X,Y) | Returns X with the characters in Y trimmed from the right side. If Y is not specified, spaces and tabs are trimmed. | rtrim(" ZZZZabcZZ ", " Z") | [trim_end()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/trimendfunction) | <pre>trim_end(@"[ Z]+",A)</pre>
searchmatch(X) | Returns true if the event matches the search string X. | searchmatch("foo AND bar") | [iif()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/iiffunction) | <pre>iif(field has "X","Yes","No")</pre>
split(X,"Y") | Returns X as a multi-valued field, split by delimiter Y. | split(address, ";") | [split()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/splitfunction) | <pre>split(address, ";")</pre>
sqrt(X) | Returns the square root of X. | sqrt(9) | [sqrt()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/sqrtfunction) | <pre>sqrt(9)</pre>
strftime(X,Y) | Returns epochtime value X rendered using the format specified by Y. | strftime(_time, "%H:%M") | [format_datetime()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/format-datetimefunction) | <pre>format_datetime(time,'HH:mm')</pre>
strptime(X,Y) | Given a time represented by a string X, returns value parsed from format Y. | strptime(timeStr, "%H:%M") |  [format_datetime()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/format-datetimefunction) | <pre>format_datetime(datetime('2017-08-16 11:25:10'),'HH:mm')</pre>
substr(X,Y,Z) | Returns a substring field X from start position (1-based) Y for Z (optional) characters. | substr("string", 1, 3) | [substring()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/substringfunction) | <pre>substring("string", 0, 3)</pre>
time() | Returns the wall-clock time with microsecond resolution. | time() | [format_datetime()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/format-datetimefunction) | <pre>format_datetime(datetime(2015-12-14 02:03:04.12345), 'h:m:s.ffffff')</pre>
tonumber(X,Y) | Converts input string X to a number, where Y (optional, defaults to 10) defines the base of the number to convert to. | tonumber("0A4",16) | [toint()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/tointfunction) | <pre>toint("123")</pre>
tostring(X,Y) | Returns a field value of X as a string. If the value of X is a number, it reformats it as a string.<br /><br />  If X is a Boolean value,, reformats to "True" or "False".If X is a number, the second argument Y is optional and can either be "hex" (convert X to hexadecimal), "commas" (formats X with commas and 2 decimal places), or "duration" (converts seconds X to readable time format HH:MM:SS). | This example returns: foo=615 and foo2=00:10:15: <br />… \| eval foo=615 \| eval foo2 = tostring(foo, “duration”) | [tostring()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/tostringfunction) | <pre>tostring(123)</pre>
typeof(X) | Returns a string representation of the field type. | typeof(12) | [gettype()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/gettypefunction) | <pre>gettype(12)</pre>
urldecode(X) | Returns the URL X decoded. | urldecode("http%3A%2F%2Fwww.splunk.com%2Fdownload%3Fr%3Dheader") | [url_decode](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/urldecodefunction) | <pre>url_decode('https%3a%2f%2fwww.bing.com%2f')</pre>
validate\| (X,Y,…) | Given pairs of arguments, Boolean expressions X and strings Y, returns the string Y corresponding to the first expression X that evaluates to False and defaults to NULL if all are True. | validate(isint(port), "ERROR: Port is not an integer", port >= 1 AND port <= 65535, "ERROR: Port is out of range") | |
 |  | | | | 

<br />
<br />
<br />
<br />

<h2><u>Common Stats Commands</u></h2>
<br />


SPL Command | Description | SPL Example | KQL | KQL Example | 
--- | --- | --- | --- | --- | 
avg(X) | Returns the average of the values of field X. | | [avg()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/avg-aggfunction) | <pre>avg(X)</pre>
count(X) | Returns the number of occurrences of the field X. To indicate a specific field value to match, format X as eval(field="value"). | | [count()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/count-aggfunction) | <pre>summarize count()</pre>
dc(X) | Returns the count of distinct values of the field X. | | [dcount()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/dcount-aggfunction) | <pre>…\| summarize countries=dcount(country) by continent</pre>
earliest(X) | Returns the chronologically earliest seen value of X. | | [arg_min()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/arg-min-aggfunction) | <pre>… \| summarize arg_min(TimeGenerated, *) by X</pre>
latest(X) | Returns the chronologically latest seen value of X. | | [arg_max()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/arg-max-aggfunction) | <pre>… \| summarize arg_max(TimeGenerated, *) by X</pre>
max(X) | Returns the maximum value of the field X. If the values of X are non-numeric, the max is found from alphabetical ordering. | | [max()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/max-aggfunction) | <pre>…\| summarize max(X)</pre>
median(X) | Returns the middle-most value of the field X. | | [percentile()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/percentiles-aggfunction) |  <pre>…\| summarize percentile(X, 50)</pre>
min(X) | Returns the minimum value of the field X. If the values of X are non-numeric, the min is found from alphabetical ordering. | | [min()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/min-aggfunction) | <pre>…\| summarize min(X)</pre>
mode(X) | Returns the most frequent value of the field X. | | [top-hitters()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/tophittersoperator) | <pre>…\| top-hitters 1 of Y by X</pre>
perc<X>(Y) | Returns the X-th percentile value of the field Y. For example, perc5(total) returns the 5th percentile value of a field "total". | | [percentile()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/percentiles-aggfunction) | <pre>…\| summarize percentile(Y, 5)</pre>
range(X) | Returns the difference between the max and min values of the field X. | | | 
stdev(X) | Returns the sample standard deviation of the field X. | | [stdev](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/stdev-aggfunction) | <pre>stdev()</pre>
stdevp(X) | Returns the population standard deviation of the field X. | | [stdevp()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/stdevp-aggfunction) | <pre>stdevp()</pre>
sum(X) | Returns the sum of the values of the field X. |  | [sum()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/sum-aggfunction) | <pre>sum(X)</pre>
sumsq(X) | Returns the sum of the squares of the values of the field X. | | |
values(X) | Returns the list of all distinct values of the field X as a multi-value entry. The order of the values is alphabetical. | | [make_set()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/makeset-aggfunction) | <pre>…\| summarize r = make_set(X)</pre>
var(X) | Returns the sample variance of the field X. | | [variance()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/variance-aggfunction) | <pre>variance(X)</pre>
 |  | | | | 

<br />
<br />
<br />

_The above SPL samples are based on [Splunk quick reference guide](https://www.splunk.com/pdfs/solution-guides/splunk-quick-reference-guide.pdf)_
