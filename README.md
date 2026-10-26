# oohttp（历史 Go HTTP 分支）

[English documentation](https://fasttls.github.io/en/ecosystem/)

Go net/http 的派生实现，允许接入不同 TLS 连接实现（例如 uTLS）。来源与上游版本见 [UPSTREAM](UPSTREAM)，原始接口和维护资料见 [上游指南](UPSTREAM_GUIDE.md)。这是 FastTLS 托管的分支，不代表 OONI 或 Go 官方对本分支的维护与背书。

当前 Fastls 请求链路使用 [ftransport](https://github.com/FastTLS/ftransport)，普通用户应从 [统一文档](https://fasttls.github.io/) 选择 Go、Python 或 Node SDK。

## 安装与最小调用

本分支 go.mod 声明 Go 1.21；这不是对所有后续 Go 版本的兼容保证。旧项目需要该实现时固定审核过的提交：

```sh
go get github.com/FastTLS/oohttp@<已审核提交SHA>
```

```go
package main

import (
    "io"
    "log"
    "time"
    http "github.com/FastTLS/oohttp"
)

func main() {
    if err := run(); err != nil { log.Fatal(err) }
}

func run() error {
    client := &http.Client{Timeout: 30*time.Second}
    response, err := client.Get("https://example.com/")
    if err != nil { return err }
    defer response.Body.Close()
    _, err = io.Copy(io.Discard, response.Body)
    return err
}
```

上面使用普通默认 TLS。要换用其它 TLS 库，需要实现 [TLSConn](tlsconn.go)，并配置传输工厂；仅改 import 不会自动获得浏览器 ClientHello。

## 支持与限制

- 提供派生 HTTP 客户端和 StdlibTransport 适配器；标准库 Request/Response 与本库类型不是完全相同的类型。
- 不包含 http/pprof 分支；httptrace 缺少依赖内部 nettrace 的部分网络事件。
- 不保证与当前 Go 标准库全部功能、安全修复、HTTP 行为或任意 TLS 库无缝兼容。
- 不提供浏览器运行时、完整浏览器指纹认证或网站成功率保证。

## 协议、贡献与反馈

保留 [LICENSE](LICENSE) 中的 OONI、Go Authors 版权及 BSD 条款，同时保留 [PATENTS](PATENTS) 专利授权。其它来源按各文件声明；本次不改变原授权。问题反馈见 [Issues](https://github.com/FastTLS/oohttp/issues)，维护步骤见 [上游指南](UPSTREAM_GUIDE.md)。
## 使用边界与免责声明

仅在拥有权限的系统、数据和网络上使用，并遵守适用法律、服务条款及隐私要求。软件按许可证约定以“现状”提供，不承诺可用性、特定用途适用性、第三方服务放行、匿名性或与真实浏览器完全一致。浏览器名称和版本用于描述配置，不表示获得相应厂商认证或背书。部署方负责访问控制、密钥和个人信息保护、请求限额及数据备份；担保排除与责任限制以随附许可证及适用法律为准。本说明不改变许可证授予的权利或新增许可限制。
