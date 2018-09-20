### SpringBoot邮件开发

1. 通过maven进行包的引入

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-mail</artifactId>
   </dependency>
   ```

2. 邮件发送信息配置

   ```properties
   #mail邮件  这个密码在邮箱设置-->账户中-->下面生成出来的
   spring.mail.host=smtp.qq.com
   spring.mail.password=XXXXXXXXXXXXXXXXXXXX
   spring.mail.username=237594169@qq.com
   spring.mail.protocol=smtp
   spring.mail.properties.mail.smtp.auth=true
   spring.mail.properties.mail.smtp.starttls.enable=true
   spring.mail.properties.mail.smtp.starttls.required=true
   ```

3. 编写测试类，发送测试邮件

   ```java
   package com.example.demoreport;
   
   import org.junit.Test;
   import org.junit.runner.RunWith;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.test.context.SpringBootTest;
   import org.springframework.mail.SimpleMailMessage;
   import org.springframework.mail.javamail.JavaMailSender;
   import org.springframework.test.context.junit4.SpringRunner;
   
   @RunWith(SpringRunner.class)
   @SpringBootTest
   public class DemoReportApplicationTests {
   
       private final Logger logger = LoggerFactory.getLogger(DemoReportApplication.class);
   
       @Autowired
       private JavaMailSender javaMailSender;
   
       @Test
       public void contextLoads() {
           SimpleMailMessage simpleMailMessage = new SimpleMailMessage();
           simpleMailMessage.setFrom("237594169@qq.com");
           simpleMailMessage.setTo("237594169@qq.com\n");
           simpleMailMessage.setSubject("主题：test");
           simpleMailMessage.setText("hello world!");
           javaMailSender.send(simpleMailMessage);
       }
   }
   
   ```


