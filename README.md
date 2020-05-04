# 百度网盘相关技术分析

## 百度秒传技术分析
各个秒传技术本质上是一样的，下面以pandl为例

### pandl的技术分析

pandl创建的秒传链接如下：
`bdpan://5oG25Lq65LygLlRoZS5HYW5nc3Rlci5UaGUuQ29wLlRoZS5EZXZpbC4yMDE5LkhENzIwUC5YMjY0LkFBQy5Lb3JlYW4uQ0hTLk1wNEJhLm1wNHwyNDQyMTE0OTczfDVlYTBhZGUxYWU0ZDY2NGRlZmVjMzI1NDA3ZjM5N2UzfDFkNjZkYTdmNTU0MmEzYTg2NWVlNDZjMTc5ZjBlMjhj`

除去`bdpan://`后面的字符串明显看出是base64编码，解密得：

`恶人传.The.Gangster.The.Cop.The.Devil.2019.HD720P.X264.AAC.Korean.CHS.Mp4Ba.mp4|2442114973|5ea0ade1ae4d664defec325407f397e3|1d66da7f5542a3a865ee46c179f0e28c`

通过符号`|`，分成4部分，简称百度网盘文件四要素。
1. 文件名
2. 文件大小(size)
3. MD5
4. slice-MD5

分享的本质如下：利用百度秒传的漏洞，分别调用秒传的接口，假装发送文件四要素，造成秒传的假象，从而达到分享的目的。

相关的代码片段如下：
```js
$.ajax({
    url: '/api/rapidupload',
    type: 'POST',
    data: {
        path: dir + file.name,
        'content-md5': try_flag?file.md5.toUpperCase():file.md5,
        'slice-md5': try_flag?file.md5s.toUpperCase():file.md5s,
        'content-length': file.size
    }
})
```