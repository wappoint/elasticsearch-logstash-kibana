# kibana安装及配置
1. 下载地址
 > https://artifacts.elastic.co/downloads/kibana/kibana-6.2.4-linux-x86_64.tar.gz

2. 解压及安装
   > /opt/kibana

3. 常见普通配置
   > 修改配置文件：
   > /opt/kibana/config/kibana.yml
   
   >> server.port: 5601
   
   >> server.host: "192.168.0.72"
   
   >> elasticsearch.url: "http://192.168.0.72:9200"
   
   >> #elasticsearch.username: "user"
   
   >> #elasticsearch.password: "pass"
   
   >> pid.file: /opt/kibana/data/kibana.pid
   
4. 启动kibana
   > /opt/kibana/bin/kibana & 