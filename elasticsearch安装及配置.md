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
        > > 请关注 Elastic 的网站，及时更新升级 Elasticsearch 的最新版本。Elasticsearch 每次版本发布都会优化和改进一部分功能，尤其是安全漏洞的补丁。仔细阅读 Elasticsearch 的更新记录，Elasticsearch 的版本遵照 语义化版本 ，所以小版本间应该是能够无缝升级的。建议及时本地测试和线上更新，升级前，记得 snapshot 做好备份。
    	
    >  2、修改默认的 Elasticsearch 集群名称
    >> Elasticsearch 默认的集群名称是 elasticsearch，请在生成环境上一定要修改成其他的名称。在不同的环境和不同的集群要保证使用不相同的名称，并且监控集群节点情况，如果有未知节点加入，一定要及时预警。
    
    >> [文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html#cluster.name)
    
    >  3、不要暴露 Elasticsearch 在公网上
    >> Elasticsearch 默认端口是 9200，绑定的是本机 127.0.0.1 的这个 IP，这个默认参数其实很安全。但是有很多人想要绑定其他的 lAN 口或者公网的 IP，并修改相应参数。
                              记住，修改有风险，如果确实需要将 Elasticsearch 暴露在公网环境，请修改特定的端口绑定 IP，不要直接修改参数： network.host，而是要分别修改：http.port
                              来绑定 HTTP 协议9200 端口的 IP（RESTful 接口调用）和修改：transport.tcp.port 对应的 TCP 9300 端口的 IP（集群内通信）。如果你不需要 Http 端口，
                              禁用掉是比较安全的做法。另外还需要在 Elasticsearch 之上加上成熟的安全防护措施（注意是成熟的！），在这里提供几种方案
                              
    >> a、9200 的 HTTP 接口之上加上 Nginx 来提供 Http Basic-Auth 的基本的身份认证，辅助 SSL 证书进行传输层的加密。并在 Nginx 进一步限制可接受 Verb 请求类型及可被操作的索引前缀。

    >> b、使用 Elastic 的 X-Pack 插件，同样提供了 Http Basic-Auth 和 SSL 传输层的加密。X-Pack 还能提供内外 Elasticsearch 集群节点间的流量加密，避免旁路攻击。
        
    >> c、[文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html#common-network-settings)
        
    >  4、禁用批量删除索引
    >> Elasticsearch 支持通过_all(全部)和通配符(*)来批量删除索引。在生产环境，可是这个有点危险，你可以通过设置： action.destructive_requires_name: true 来禁用它。
        
    >  5、安全使用动态脚本
    >> Elasticsearch 的脚本很方便的可以对数据进行操作，不过如果你暂时没有用上，还请禁用（Elasticsearch 在1.2.x 以后已经默认禁用了）。如果你已经在使用动态脚本，比如 Groovy，它不是沙盒机制的脚本引擎，启用 inline 或 store 类型的 groovy 会有安全风险，所以请限制脚本的接触方。比如通过模板的方式来限制脚本的调用，只需要执行特定预先定义好的脚本，对调用参数进行过滤和参数值的检测，做好验证。同时对各种日志都必须要保留好，方便进行日志分析，异常的调用和请求一定要有发现和预警机制。
    >>> a、Elasticsearch 默认启用了 Java Security Manager ，但还请正确配置其白名单。
       
    >>> b、使用 Groovy 或者 JavaScript 等脚本的用户，请尽快迁移到 Painless 脚本，Painless 更快更安全。
    
    >>> c、[文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting-security.html)
    
    > 6、给 Elasticsearch 服务器加固
    >> 服务器加固是一个必备流程，不管上面运行的是什么服务；
    
    >>> a、首先，请开启防火墙，并设置防火墙规则为只开启必备的端口。完成之后，使用扫描工具扫描服务器，检查端口开发情况。
    
    >>> b、如果可能，不要用密码的方法来远程登录服务器，尽可能使用公私钥的方式来 SSH 登录服务器。如果只能使用密码，请妥善保管好你的用户名和密码，禁用 root 用户，不用使用弱密码。
    
    >>> c、关注 Java 最新的漏洞，使用安全的 JVM 运行。
    
    >>> d、注意服务器及时更新最新的软件，使用安全的 repo 软件源。绑定软件源的 HOST 和 IP，避免 DNS 污染造成的攻击，关注服务器软件漏洞，及时打上补丁。
    
    >>> e、收集系统日志和安装相应的入侵检测软件，及时发现服务器是否有异常行为。
    
    > 7、不要以 root 身份运行 Elasticsearch
    >> 记住一定不要以 root 身份来运行 Elasticsearch。另外，不要和其他的服务公用相同的用户，然后还要把用户的权限最小化。
    
    >>范例：
    
    >>> sudo -u es-user ES_JAVA_OPTS="-Xms1024m -Xmx1024m"
    
    >>> /opt/elasticsearch/bin/elasticsearc

    > 8、正确设置 Elasticsearch 的数据目录
    >> 请确保 Elasticsearch 的目录分配了合理的读写权限，避免使用共享文件系统，确保只有 Elasticsearch 的启动用户才能访问。同样，日志目录也一样需要正确配置，避免泄露敏感信息。
    
    >> [文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html#path-settings)
    
    > 9、定期对 Elasticsearch 进行备份
    >> 使用 Elasticsearch 提供的备份还原机制，定期对 Elasticsearch 的数据进行快照备份，以备不时之需。
    
    >> [文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html)
    
    > 10、加上监控和预警
    >> Elasticsearch 提供了很好的默认参数，对参数方面还做了正确性检测，bootstrap 启动检查。不准确的参数，直接不允许 Elasticsearch 启动。作为用户，你还需要收集各种日志和系统监控信息，及时主动掌握服务健康和资源使用情况，发现异常情况要及时处理，这里提供一些方案：
    
    >>> a、使用开源的 Elastic Stack 收集这些日志。可以使用 Filebeat 收集日志，Metricbeat 收集系统监控信息，存进 Elasticsearch。一旦发现异常的波动，使用 Watcher 来进行预警，通过邮件或者 webhook 调用短信、微信或者电话。
    
    >>> b、使用其他厂商的安全监控产品。
    
    >>> c、使用托管的 Elasticsearch 云的产品，如 Elastic Cloud等等。
5. 启动elasticsearch
   > /opt/elasticsearch/bin/elasticsearch &
   
   >> 1、使用非root用户启动elasticsearch
   
   >> 2、配置文件描述符
   >>> echo "  " \>> /etc/security/limits.conf
   
   >>> echo "#limit dinghe add 20170828 " \>> /etc/security/limits.conf
   
   >>> echo "* soft nofile 65536" \>> /etc/security/limits.conf
   
   >>> echo "* hard nofile 65536" \>> /etc/security/limits.conf
   
   >>> 执行生效命令 sysctl -p
   
   >> 3、配置map_counter
   >>> echo "#elasticsearch inti dinghe add 20170828" \>> /etc/sysctl.conf
   
   >>> echo "vm.max_map_count = 262144" \>> /etc/sysctl.conf

6. 安装插件
   > 安装ik分词插件
   >> /opt/elasticsearch/bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.4/elasticsearch-analysis-ik-6.2.4.zip