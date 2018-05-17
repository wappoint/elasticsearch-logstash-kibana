# logstash安装及配置
1. 下载地址
 > https://artifacts.elastic.co/downloads/logstash/logstash-6.2.4.tar.gz

2. 解压及安装
   > /opt/logstash

3. 常见普通配置
   > 修改配置文件：
   > /opt/logstash/config/startup.options
   
   >> LS_HOME=/opt/logstash/
   
   >> LS_SETTINGS_DIR="${LS_HOME}/config"
   
   > /opt/logstash/config/logstash.yml
   
   >> http.host: "192.168.0.72"
   
   >> path.logs: /opt/logstash/data/logs
   
   > 增加输入|输出配置文件目录(pipeline)
   
   >> mkdir /opt/logstash/config/conf.d -p 
   
   ```
   input {
     file {
       type => "test-log"
       path => "/opt/logstash-tutorial-dataset"
     }
     beats {
      host => "192.168.0.72"
      port => 5044
     }
   }
   output {
     elasticsearch {
       hosts => ["192.168.0.72:9200"]
     }
   }
   ``` 
   
4. 启动logstash
   > /opt/logstash/bin/logstash -f /opt/logstash/config/conf.d/ & 
   
5. 增加logstash-input-jdbc插件
   > /opt/logstash/bin/logstash-plugin install logstash-input-jdbc
   
   