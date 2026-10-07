# Krak / Kraken / Kraken Pro：Loon 英国固定分流

本目录是可发布的规则与使用说明，包含3条域名规则及16条条件规则。三款 App 使用同一个手动英国策略。规则来自本地抓包分析；不包含抓包、账户凭据、机场订阅或 MITM 私钥。

## 使用方式

在自己的主配置中保留英国动态订阅筛选以及：

```ini
[Proxy Group]
Kraken英国固定 = select,英国节点
```

将下列实际引用行放到 `[Remote Rule]` 的首部，位于通用代理/金融规则之前。不要改动 LAN、CN REGION 的相对顺序。

```ini
[Remote Rule]
https://raw.githubusercontent.com/daixianglong199-dotcom/loon-kraken-routing/main/Kraken-UK.list, policy=Kraken英国固定, tag=Kraken UK, enabled=true
```

如果此前采用了本项目的本地主配置，把 `[Rule]` 中从 `# BEGIN Kraken UK` 到 `# END Kraken UK` 的整块删除，避免本地旧规则优先于远程更新。原来的其他本地规则、FINAL、英国筛选及 select 策略保留。

在组内手动选择一个实际英国节点。没有自动切换、节点参数静态快照或机场订阅修改。

## 条件规则的边界

用户已明确授权优先保持 Kraken 系列的英国出口：对 `sdk-service.nsureapi.com` 使用精确 DOMAIN 例外，不再要求 UA。其他 App 使用这个相同主机时也会走英国；没有添加 `DOMAIN-SUFFIX,nsureapi.com`。现有 `sdk.nsureapi.com` UA 条件不变。主配置 `policy=Kraken英国固定` 为该订阅统一指定策略，因此 list 中写 `DOMAIN,sdk-service.nsureapi.com`。

本地主配置中同一个 HOST 的精确英国规则可以保留，它优先命中并与远程规则同策略，避免原有本地/插件规则抢先匹配。

- 需要 Loon 3.1.7及以上。Loon 公开文档允许把受支持的规则类型放入订阅规则文件，包括逻辑和 HTTP 规则；文件末尾不写策略，策略由主配置的 policy 映射。
- HTTPS 的 UA/完整 URL 必须对规则引擎可见，通常依赖成功解密；本规则文件不启用或改变 MITM。仅凭抓包时曾看到 Header 不能保证日常模式仍能看到。
- 本地规则 > 插件规则 > 订阅规则。改成 Remote Rule 后，规则来源优先级会下降；如果原插件或本地规则先覆盖共享服务，必须在请求记录中检查实际命中。把该订阅放在首位无法覆盖更高来源优先级。
- 如果要保持先前本地条件规则的来源优先级，采用混合方式：远程文件只保留开头3条域名规则，16条条件规则继续留在 `[Rule]`。不要同时采用完整远程规则文件和整块本地规则。
- 没有 Sardine、Stripe、Expo、Sentry、Braze、Segment 等共享服务整域永久英国规则。Sardine 通用 WebView、LaunchDarkly、Privy 等无法安全识别的请求继续原分流。
- Sentry 仅匹配三个由 App release/包名核对的项目 envelope 端点。项目 URL 是服务目的地，不是操作系统来源 App 判断；若项目被其他 App 复用，需要重新核对。

## 验证与更新

在 Loon 中刷新这个订阅规则，重新建立连接，然后分别冷启动三个 App。检查请求详情中的 UA/URL、命中规则、策略和最终节点；其他 App 使用相同服务时应保持原分流。

原始离线检查（本次 HOST 例外添加前）：1,154条导出请求，934条域名/专属客服租户识别、85条条件识别、135条保留原规则；这些数字不代表手机实际命中率。尚未执行 iOS 原生解析测试。

后续修改 GitHub 上的规则文件并保存提交后，在 Loon 更新订阅规则即可获取新内容；如果启用定期更新则由 Loon 按设置刷新。

## 文档依据

- [Loon 订阅规则](https://nsloon.app/docs/Rule/sub_rule/)
- [Loon 逻辑规则](https://nsloon.app/docs/Rule/logic_rule/)
- [Loon HTTP 规则](https://nsloon.app/docs/Rule/http_rule/)
- [Loon 规则优先级](https://nsloon.app/docs/Rule/)

## 发布范围

仅发布本目录下的 `Kraken-UK.list` 和 `README.md`。完整主配置、备份、ZIP 抓包、解压数据和详细私人报告均留在本地。
