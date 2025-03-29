<!-- ##{"timestamp":1743254206}## -->

# 简单步骤总结

1. 域名配置 DNS 指向服务器
2. 按需设置 Page Rules 或 Redirect Rules
3. 耐心等待生效


# 域名配置 DNS 指向服务器

假设计划从 `a.com` 重定向去 `b.com`

重定向规则设定状态下必须在 DNS 记录添加最少一个指定的服务器才能生效（曾经的 Google Domains 没这个要求）

设定 DNS 的可以直接是根域名 `a.com` 或仅某个用来指向的三级域名 `www.a.com`，看情况选择
这个指定的服务器可以是任意的
- Cloudflare 的虚拟 IP: 192.0.2.1 搭配 proxied（没服务器时推荐，防止不小心跳转到不相关服务器上）或 1.1.1.1 搭配 unproxied（不推荐） 或其它类似 GitHub Pages 的4个 IP 段搭配 proxied
- 根域名 `a.com` 状态： A - @ - 192.0.2.1 - proxied，此时其它三级域名可用 CNAME - www - @ - proxied 来指向根域名也用来重定向到任意指定位置
- 某个仅用来重定向的三级域名 `www.a.com` 状态：A - www - 192.0.2.1 - proxied，此时根域名和其它域名可以用来做别的（没必要，因为用来做别的话本来就已经有指定的服务器了）


# 方法1：Page Rules（推荐）

传统方式，规则简单，免费账号最多支持3条（可顺便直接在此设置强制或自动跳转 HTTPS）
默认自带保留查询字符串（Preserve query string）功能

Rules - Page Rules - Create Page Rule

URL (required): `a.com/*`
Then the settings are: `Forwarding URL` - Select status code (required): `302 Temporary Redirect`
Enter destination URL (required): `https://b.com/$1`

Save Page Rule

这样 `a.com/任意内容` 都会被重定向去 `https://b.com/任意内容`


# 方法2：Redirect Rules

新出的方式，规则可以更复杂功能更多，免费账号最多支持10条，搭配3条传统的 Pages Rules 总共可以13条，良心
所有状态下被重定向指向的域名必须加上 http:// 或 https://（推荐）

## Wildcard pattern（推荐）
想用带通配符功能的 wildcard 如 `https://a.com/*` 重定向去 `https://b.com/${1}`，就只能用1对1的单个域名重定向的 Wildcard pattern
此时输入域名规则必须严格一致，如设定了 `http://a.com/*`，浏览器输入域名时就必须是带 http:// 开头才能生效
但是若设定了 `https://a.com/*`，则浏览器输入 http:// 或  https:// 都能生效
此功能包含了 `a.com` 重定向到 `https://b.com/`

Rules - Create rule - Redirect Rule

Rule name (required): `a.com to b.com`（随便一个易记的名）

If incoming requests match…: `Wildcard pattern`

Request URL: `https://a.com/*`

Then...
Target URL: `https://b.com/${1}` - Status code: `302`
`Preserve query string`: 勾选（重定向时保留保留查询字符串，也就是链接内包含 ? 后的完整URL）

Place at
Select order: `First`（按需选择顺序）

Deploy


## Custom filter expression

适用于仅重定向域名本身，无视域名前后的额外内容，支持通过 Or 实现多个三级域名转到指定域名上
如 `a.com` 和 `www.a.com` 都重定向去 `b.com`（也可以 DNS 内 CNAME 直接 www 或其它前缀直接指向 @）
或 `1.a.com` 和 `2.a.com` 都重定向去 `b.com`

Rules - Create rule - Redirect Rule
Rule name (required): `a.com to b.com`（随便一个易记的名）

If incoming requests match…: `Custom filter expression`

Field: `Hostname `- Operator: `equals` - Value: `a.com`
Or
Field: `Hostname` - Operator: `equals` - Value: `www.a.com`

Then...
URL redirect
Type: `Static` - URL: `https://b.com/` - Status code: `302`
`Preserve query string`: 勾选（重定向时保留链接内包含 ? 后的完整URL）

Place at
Select order: `First`（按需选择顺序）

Deploy

# 小知识
有时候设置完可能需要等一段时间才能生效，一般是0-60分钟以内，因此不要以为设置错了心急频繁改

 - 301 和 302 区别（教程选了 302）
用 301：当你确定旧 URL 不再使用，允许浏览器缓存，且希望将流量和 SEO 价值永久转移到新地址。
用 302：当你只是暂时调整流量，不需浏览器缓存，或者不确定未来是否恢复旧 URL。

 - Preserve Query String 和 Dynamic 区别（教程选了 Preserve Query String + Static）
Preserve Query String：就像一个开关，打开就保留查询字符串，关闭就不保留，功能单一，是 Dynamic 的一个子集功能。
Dynamic：像一个工具箱，你可以用它拼装出任何你想要的目标 URL，能做的事情远超 Preserve Query String，它不仅能保留查询字符串，还能做更多自定义操作。

