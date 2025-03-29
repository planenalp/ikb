<!-- ##{"timestamp":1741005660}## -->

# 1. Cloudflare 首次添加 
在 Cloudflare 首次添加两个 CNAME 时，Proxy status 必须关闭，否则 Blogspot 内无法确认，添加成功后可重新开启

实测无法通过 Cloudflare 的 CDN 来实现墙内加载图片，开关这个 Proxy Status 选项都无效

![Image](https://github.com/user-attachments/assets/3b4eb855-cff5-498c-9b42-d618bad0eecc)

//顺便补充一条，域名在 Google Domains 内的状态指定给同一个账号下的 Blogger 账号，可以直接于 Blogger 内指定域名跳转不需额外设置 CNAME，除非给了 Cloudflare 托管


# 2. 重定向
想要实现 xxx.com 重定向到 www.xxx.com
可以直接在 DNS 记录像之前的 www 那样添加一条CNAME记录 @ 到 ghs.google.com 同时搭配开启 Blogger 内的自动跳转www选项（必须两个都做）

Settings - Publishing - Redirect domain

或按照下面网址步骤做https://support.google.com/blogger/answer/1233387

添加以下 4 个指向 Google IP 的 A 记录。如果“mydomain.com”中已有 A 记录，则您应移除现有的 A 记录。

216.239.32.21
216.239.34.21
216.239.36.21
216.239.38.21
除非默认指定就不是用 www 的三级域名，比如 blog.godpan.com 就不需要这条设置


# 3. 证书
SSL/TLS 的证书那里，Flexible/Full/Full (strict)三个似乎都可以，不同教程的人说的都不同
理论上应该最后那个最好


# 4. 延迟生效
设置完不是马上生效的，需要等最长24小时，一般一个小时以内也能看到了


# 5. 多次重新添加
曾经添加过的域名，因各种原因中途取消并删除域名商内的DNS记录后，重新添加同一个域名时不会再弹出CNAME记录指导

此时可以进入下面网址找到对应内容
https://www.google.com/webmasters/verification/home

解决办法源自：
https://support.google.com/blogger/thread/120124946/how-do-i-reactivate-my-custom-domain-in-blogger?hl=en

