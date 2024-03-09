# 一、CFW的规则编辑
目前 Clash 支持的规则类型如下：

* DOMAIN-SUFFIX：域名后缀匹配
* DOMAIN：域名匹配
* DOMAIN-KEYWORD：域名关键字匹配
* IP-CIDR：IP 段匹配
* SRC-IP-CIDR：源 IP 段匹配
* GEOIP：GEOIP 数据库（国家代码）匹配
* DST-PORT：目标端口匹配
* SRC-PORT：源端口匹配
* PROCESS-NAME：源进程名匹配
* RULE-SET：Rule Provider 规则匹配
* MATCH：全匹配
### TIP

> 使用PROCESS-NAME需要 Clash for Windows v0.11.5 及以上版本；规则需要匹配完整的进程名（包括可执行文件后缀）方可生效；该规则不适用于 TAP 流量
> 
> 用户界面暂不支持添加RULE-SET规则，请手动编辑配置文件
> 
> MATCH需要位于规则列表末尾


# 二、CFW的配置文件预处理
## 版本要求
0.10.1 版本更新后，支持使用 JavaScript 对下载后的配置文件进行预处理

0.11.10 版本更新后，支持使用 YAML 对下载后配置文件进行简单预处理

## 简便方法（YAML）
如果需要对下载地址为https://example.com/profile.yaml的配置文件进行预处理，操作如下：

1. 进入 Settings 界面
2. 滚动至 Profiles 栏
3. 点击 Parsers 右边 Edit 打开编辑器，填入：
```yaml
parsers: # array
  - url: https://raw.githubusercontent.com/families/rules_scripts/main/Clash/Config/Lazy_Config.yaml
    yaml:
      prepend-rules:
        - DOMAIN,test.com,DIRECT # rules最前面增加一个规则
        - PROCESS-NAME,test.exe,DIRECT # 自定义规则，源进程名匹配
      mix-proxy-providers: # 使用CFW的Diff功能自动修改订阅url更简单
        All: # proxy-provider属性要完整替换
          type: http
          url: "你的订阅链接" # 有无双引号都可以
          interval: 86400
          path: ./all.yaml
          health-check:
            enable: true
            interval: 18000
            url: http://www.gstatic.com/generate_204

```
4. 点击编辑器右下角保存按钮
当配置文件触发刷新（包括自动更新）时，CFW 会读取yaml字段定义的值，将对应值插入/合并到原配置文件中

## 参数说明
|键	|值类型	|操作|
| :----- | :--: | -------: |
|append-rules|	数组	|数组合并至原配置rules数组后|
|prepend-rules|	数组|	数组合并至原配置rules数组前|
|append-proxies	|数组|	数组合并至原配置proxies数组后|
|prepend-proxies|	数组	|数组合并至原配置proxies数组前|
|append-proxy-groups	|数组	|数组合并至原配置proxy-groups数组后|
|prepend-proxy-groups	|数组	|数组合并至原配置proxy-groups数组前|
|mix-proxy-providers|	对象	|对象合并至原配置proxy-providers中|
|mix-rule-providers|	对象	|对象合并至原配置rule-providers中|
|mix-object	|对象	|对象合并至原配置最外层中|
|commands	|数组	|在上面操作完成后执行简单命令操作配置文件|


# 三、CFW的Mixin
## 版本要求
0.9.5 版本更新后，支持向所有配置文件中注入公共属性设置

## 配置文件
例如：在配置文件中统一添加dns字段，操作如下：

1. 进入 Settings 页面
2. 滚动至 Profile Mixin 栏
3. 点击 YAML 右边 Edit 小字打开编辑界面
4. 在修改编辑界面内容为：
```yaml
mixin: # 注意下面缩进
  dns:
    enable: true
    listen: :53
    nameserver:
      - 8.8.8.8
```
5. 点击编辑器右下角保存
在启动或切换配置时，上面内容将会替换到原有配置文件中进行覆盖

### TIP

> 配置文件内容不会被修改，混合行为只会发生在内存中
> 
> 可以通过任务栏图标菜单开关这个行为

# 四、CFW的Diff功能
可以自定义修改在线配置文件某些内容，当更新拉取配置文件时会自动merge之前修改内容到新配置文件

# 参考
https://docs.cfw.lbyczf.com/contents/ui/profiles/rules.html#%E8%A7%84%E5%88%99%E7%BC%96%E8%BE%91
https://docs.cfw.lbyczf.com/contents/parser.html#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E9%A2%84%E5%A4%84%E7%90%86
https://docs.cfw.lbyczf.com/contents/mixin.html#mixin
