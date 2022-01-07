# USTB-daily-report
Auto daily health report 自动平安报

## WARNING
**“平安报”上报内容是学校决策的重要参考之一，务必认真对待。**

## Features

- [x] 自动平安报
- [x] 自动续SESSION
- [x] 日志记录
- [x] 邮箱示警
- [x] 随机延迟（否则每天定时上报有点可疑（？））
- [ ] 免爬虫获取JSESSIONID
- [ ] 免爬虫获取上内容

## 一种集成到 Home Assistant (HA) 上的实现

[ustb-daily-with-hass](https://github.com/terrance-liang/ustb-daily-with-hass)

## Collect Data First
首先，你需要对平安报请求进行一次抓包，具体方法参见[这篇文章](https://blog.vincenteliang.cn/2020/05/31/ustb_checkIn_auto#%E6%8A%93%E5%8C%85-packet-capture)。
抓包的请求的报文大概如下所示：
```
POST /app.RSPWxClient/index.jsp HTTP/1.1
Host: isport.ustb.edu.cn
...
```
在报文中你需要获得 Cookie 中 `JSESSIONID` 的值和 POST 请求传递的内容。
`JSESSIONID`是一个长度为 32 的字符串；POST data 的开头如下所示：
```
m=yqinfo&c=index&a=submit
```

## Edit Configurations

在本目录的 `ustb-report` 文件内填写 `REPORT_SESSION` ，其值即为 `JSESSIONID` 的值。
然后在家目录创建 `report.data` 文件，填写平安报 POST data。
```
echo "平安报 POST 内容" >~/report.data
```
如果你想使用自定义的文件名，你还需要把 `REPORT_DATA` 修改成相应形式。
其他详细设置见文件 `ustb-report` 开头。

## Add Cron tasks
在命令行输入以下指令，
```
$ crontab -e
```
在文件结尾加入
```
*/20 * * * * /path/to/ustb-report ping
30 6 * * * /path/to/ustb-report submit
```
其中 `/path/to/ustb-report` 是脚本的路径，注意，其必须是**绝对路径**。
第一行的意思是每 20 分钟续一次 `SESSION`，因为该 `SESSION` 30 分钟无动作自动过期。
第二行的意思是每天 6:30 的时候提交平安报，你也可以根据自己的需求自由修改。

## DEBUG
将脚本开头的 `DEBUG` 置 1，则调试信息不会输出到日志，而是输出到 stderr。

## Licence

This project is under MIT licence.

        Permission is hereby granted, free of charge, to any person obtaining a copy
        of this software and associated documentation files (the "Software"), to deal
        in the Software without restriction, including without limitation the rights
        to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
        copies of the Software, and to permit persons to whom the Software is
        furnished to do so, subject to the following conditions:

        The above copyright notice and this permission notice shall be included in all
        copies or substantial portions of the Software.