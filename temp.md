添加 iptables 限制后， tcpdump 是否能抓到包 ，这要看添加的 iptables 限制条件：

如果添加的是 INPUT 规则，则可以抓得到包
如果添加的是 OUTPUT 规则，则抓不到包
网络包进入主机后的顺序如下：

进来的顺序 Wire -> NIC -> tcpdump -> netfilter/iptables
出去的顺序 iptables -> tcpdump -> NIC -> Wire
