---
date: '2017-01-18T09:45:03+09:00'
layout: post
published: true
qiita_article_id: 801c7b44cb38a635060e
tags:
- Java
- spring
- spring-boot
- spring-batch
- spring-data-jpa
title: '[Java][Spring][Spring Batch] Spring Batchのメタデータテーブルを作らせない/使わせない'
updated: '2017-01-18T09:45:03+09:00'

---
# 前提  
  
Spring Batchは、ジョブやらなんやらの状態を保存するために、データベースを使うようになっています。  
  
普通には便利なんだと思われますが、そこまで重たいバッチじゃないんだよぉぉ、というときにはちと邪魔です。  
  
そんなテーブルを使わない・作らせない方法です。  
  
こちらの記事が大変参考になりました。 :smile:  
[Spring Batch でメタテーブルを使いたくないのに使用されてしまう時はこうする](http://qiita.com/blackawa/items/e9eaa254cbe27e257e10)  
  
## 環境  
  
- Spring Boot 1.2.7  
- (Spring Batch 3.0.5)  
  
# テーブルを作らせない方法  
  
テーブルの作成は、Spring Bootが自動構成するなかで行われています。  
  
よくできてますねー。  
  
でも、今回は省略。  
  
プロパティファイルかyamlファイルに、以下の設定を追加します。  
  
**application.properties**  
```properties:application.properties
spring.batch.initializer.enabled=false
```  
  
# テーブルを使わせない方法  
  
こちらの記事をどうぞ。  
[Spring Batch でメタテーブルを使いたくないのに使用されてしまう時はこうする](http://qiita.com/blackawa/items/e9eaa254cbe27e257e10)  
  
あるいは、自前でBatchConfigurerクラスを作ってしまってもいいかもしれません（自分は遠回りしたので作ってしまいました）。  
  
**MyBatchConfigurer.java**  
```java:MyBatchConfigurer.java
import javax.annotation.PostConstruct;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.batch.core.configuration.BatchConfigurationException;
import org.springframework.batch.core.configuration.annotation.BatchConfigurer;
import org.springframework.batch.core.explore.JobExplorer;
import org.springframework.batch.core.explore.support.MapJobExplorerFactoryBean;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.batch.core.launch.support.SimpleJobLauncher;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.batch.core.repository.support.MapJobRepositoryFactoryBean;
import org.springframework.batch.support.transaction.ResourcelessTransactionManager;
import org.springframework.stereotype.Component;
import org.springframework.transaction.PlatformTransactionManager;

/**
 * データベースなしのJobRepositoryを使う
 *
 */
@Component
public class MyBatchConfigurer implements BatchConfigurer {

    private static final Log LOG = LogFactory.getLog(MyBatchConfigurer.class);

    private PlatformTransactionManager transactionManager;
    private JobRepository jobRepository;
    private JobLauncher jobLauncher;
    private JobExplorer jobExplorer;

    @PostConstruct
    public void initialize() {
        if (this.transactionManager == null) {
            LOG.info("Create ResourceLessTransactionManager.");
            this.transactionManager = new ResourcelessTransactionManager();
        }

        try {
            MapJobRepositoryFactoryBean repoFactory
                    = new MapJobRepositoryFactoryBean(this.transactionManager);
            repoFactory.afterPropertiesSet();
            this.jobRepository = repoFactory.getObject();

            MapJobExplorerFactoryBean explFactory
                    = new MapJobExplorerFactoryBean(repoFactory);
            explFactory.afterPropertiesSet();

            this.jobExplorer = explFactory.getObject();
            this.jobLauncher = createJobLauncher();
        } catch (Exception ex) {
            LOG.fatal(ex.getMessage(), ex);
            throw new BatchConfigurationException(ex);
        }

    }

    protected JobLauncher createJobLauncher() throws Exception {
        SimpleJobLauncher launcher = new SimpleJobLauncher();
        launcher.setJobRepository(jobRepository);
        launcher.afterPropertiesSet();
        return launcher;
    }

    @Override
    public JobRepository getJobRepository() throws Exception {
        return jobRepository;
    }

    @Override
    public PlatformTransactionManager getTransactionManager() throws Exception {
        return transactionManager;
    }

    @Override
    public JobLauncher getJobLauncher() throws Exception {
        return jobLauncher;
    }

    @Override
    public JobExplorer getJobExplorer() throws Exception {
        return jobExplorer;
    }
}
```  
  
# Spring Bootにまつわる落ち穂拾い  
  
## Batch Auto Configurationは無効にする？  
  
無効にすると、作らなくちゃいけないクラスがいろいろ出てきたので、自分は有効にしました。  
  
## Data Source Auto Configuration有効でもOK？  
  
Batch Configurerを自前で用意したのでOKみたいです。  
