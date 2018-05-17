# elasticsearch安装及配置
1. 下载地址
   > https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.4.tar.gz
   
2. 解压及安装
   > /opt/elasticsearch
        
3. 常见普通配置
   > 修改配置文件：
   > /opt/elasticsearch/config/elasticsearch.yml
   >> cluster.name: elastic-application
   
   >> node.name: elastic-node1
   
   >> path.data: /opt/elasticsearch/data
   
   >> path.logs: /opt/elasticsearch/logs
   
   >> bootstrap.memory_lock: false
   
   >> network.host: 192.168.0.72
   
   >> http.port: 9200
   
4. 安全认证配置
    >  1、使用最新的 Elasticsearch
        >> 请关注 Elastic 的网站，及时更新升级 Elasticsearch 的最新版本。Elasticsearch 每次版本发布都会优化和改进一部分功能，尤其是安全漏洞的补丁。仔细阅读 Elasticsearch 的更新记录，Elasticsearch 的版本遵照 语义化版本 ，所以小版本间应该是能够无缝升级的。建议及时本地测试和线上更新，升级前，记得 snapshot 做好备份。
    	
    >  2、修改默认的 Elasticsearch 集群名称
        > > Elasticsearch 默认的集群名称是 elasticsearch，请在生成环境上一定要修改成其他的名称。在不同的环境和不同的集群要保证使用不相同的名称，并且监控集群节点情况，如果有未知节点加入，一定要及时预警。
    
    >  3、不要暴露 Elasticsearch 在公网上
        > Elasticsearch 默认端口是 9200，绑定的是本机 127.0.0.1 的这个 IP，这个默认参数其实很安全。但是有很多人想要绑定其他的 lAN 口或者公网的 IP，并修改相应参数。记住，修改有风险，如果确实需要将 Elasticsearch 暴露在公网环境，请修改特定的端口绑定 IP，不要直接修改参数： network.host，而是要分别修改：http.port 来绑定 HTTP 协议9200 端口的 IP（RESTful 接口调用）和修改：transport.tcp.port 对应的 TCP 9300 端口的 IP（集群内通信）。如果你不需要 Http 端口，禁用掉是比较安全的做法。另外还需要在 Elasticsearch 之上加上成熟的安全防护措施（注意是成熟的！），在这里提供几种方案： 
        
        >>  1、9200 的 HTTP 接口之上加上 Nginx 来提供 Http Basic-Auth 的基本的身份认证，辅助 SSL 证书进行传输层的加密。并在 Nginx 进一步限制可接受 Verb 请求类型及可被操作的索引前缀。

        >>  2、使用 Elastic 的 X-Pack 插件，同样提供了 Http Basic-Auth 和 SSL 传输层的加密。X-Pack 还能提供内外 Elasticsearch 集群节点间的流量加密，避免旁路攻击。