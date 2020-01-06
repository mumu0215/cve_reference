# Details
When I check the database operation file source in the controller, I found that this function does not perform any security checks on the incomming parameters.

Vulnerable source code is located on lines 60 and 61 of file webroot\app\admin\controller\system\SystemDatabackup.php
![code.jpg](/src/crmeb/code.jpg)

You can see that the $tablename parameter is directly taken from the user request without any filtering. The vulnerability calls the function Db::query () as a method in the framework, which is used to output and execute the native SQL statement. It also does not perform any filtering on the incoming parameter $tablename and directly splices the SQL statements, resulting in the generation of SQL injection.

Vulnerable URL:http://localhost/admin/system.systemdatabackup/seetable?tablename=1

Cookies are required for successful administrator login during exploit.Here is the SQL Injection payload:
http://localhost/admin/system.systemdatabackup/seetable?tablename=1' union all select 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,CONCAT(0x7e,(select user()),0x7e),21,22--+
