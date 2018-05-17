# logstash安装及配置
1. 下载地址
 > https://artifacts.elastic.co/downloads/logstash/logstash-6.2.4.tar.gz

2. 解压及安装
   > /opt/logstash

3. 常见普通配置
   > 修改配置文件：
   > /opt/logstash/config/startup.options
   > > LS_HOME=/opt/logstash/
   > > LS_SETTINGS_DIR="${LS_HOME}/config"
   
   > /opt/logstash/config/logstash.yml
   > > http.host: "192.168.0.72"
   > > path.logs: /opt/logstash/data/logs
   
   > 增加输入|输出配置文件目录(pipeline)
   > > mkdir /opt/logstash/config/conf.d -p 
   
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
   
   ```
   input {
    jdbc {  
      # mysql 数据库链接,test为数据库名  
      jdbc_connection_string => "jdbc:mysql://192.168.0.70:3306/database?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&useSSL=true"  
      # 用户名和密码  
      jdbc_user => "username"  
      jdbc_password => "12345678"  
      # 驱动 文件路径 
      jdbc_driver_library => "/opt/logstash/lib/mysql/mysql-connector-java-5.1.45.jar"  
      # 驱动类名  
      jdbc_driver_class => "com.mysql.jdbc.Driver"  
      jdbc_paging_enabled => "true"  
      jdbc_page_size => "50000"  
      # 执行的sql 文件路径+名称   
      statement_filepath => "/opt/logstash/scheduled/mysql/biz_user.sql"  
      # 设置监听间隔  各字段含义（由左至右）分、时、天、月、年，全部为*默认含义为每分钟都更新  
      schedule => "* * * * *"  
      # 索引类型  
      type => "jdbc"  
    }  
  }
  output {
    elasticsearch { 
     # ES的IP地址及端口
     hosts => ["192.168.0.72:9200"] 
     # 索引名称  
     index => "biz_user"
     # 自增ID  
     document_id => "%{id}"
     #文档类型
     document_type => "biz_user"
    }
   }
   ```
   
   