# Details
Source code file:webroot/application/controllers/admin/themes.php
![code.jpg](/src/cve/Snipaste_2020-01-16_13-30-15.png)
**Line187-212:** Determine whether the action in the POST package is “templateuploadimagefile”, and determine whether the file size is legal;

**Line213-221:** Call the validateImage() method in the web root directory/application/core/LSYii_ImageValidator.php to verify that the file type is valid. Follow up on this function:
![code2.jpg](/src/cve/Snipaste_2020-01-16_13-36-39.png)
There are two checks in this function implementation, located on lines 63 and 64:

**(1)** Line 63 checks the file type returned by the CFileHelper::getMimeType($path) function. The $path parameter comes from one of the fields(called “tmp_name”) in the PHP’s global variable--$_FILES. $path which is the cache file of the uploaded file actually has a “.tmp” file extension. In this way, you only need to upload a legitimate picture that contains a php statement(a picture webshell), which can cause the type to be detected as a picture type and bypass the verification; 

**(2)** Line 64 checks the type field of the $ _FILES variable. Details of $_FILES are as follows:
![def.jpg](/src/cve/Snipaste_2020-01-16_18-07-04.png)
The value of the “type” field is the Content-Type field in the POST packet. As long as the content of this field is specified as “Content-Type: image / jpeg”, the check can be bypassed.

**Line228、229、233:** The purpose of these codes is to handle the location of the uploaded file. **Line 228** calls the sanitize_filename() function to handle the abnormal characters except the file extension in the uploaded file name; **Line 229** directly splices the file name and the upload path without processing dangerous file extensions; **Line 233** calls the native function move_uploaded_file() to move the temporary cache file to the spliced address.

Based on the above analysis, as long as a legal format picture containing a PHP Trojan statement is made and the file extension of the second file in the POST package is changed from file extension of picture to ‘.php’, the Trojan file can be uploaded to the target server to achieve a remote code execution vulnerability.

# Exploit

The exploit is located in the background home page-> theme module-> "theme options" button-> upload button for any theme. The attack is exploited as follows:

![exploit.jpg](/src/cve/Snipaste_2020-01-16_15-35-38.png)

The successful exploitation of the attack is as follows:

![successus.jpg](/src/cve/Snipaste_2020-01-16_15-39-42.png)
