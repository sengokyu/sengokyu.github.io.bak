---
date: '2017-04-21T00:26:41+09:00'
layout: post
published: true
qiita_article_id: c8d1dd1f1c0816711c98
tags:
- Java
- spring
- spring-boot
- spring-data-jpa
title: '[Java][Spring] Spring DataのRepositoryをインスタンス化する際にHibernateのフィルタを有効にしようとして失敗した記録'
updated: '2017-04-21T00:28:29+09:00'

---
# 動機  
  
Hibernate 3でマルチテナントをしようとしました。  
  
各テーブルにテナントを区別するためのSystemId列を付けました。  
  
SystemId列を検索条件に付与するHibernateフィルタをJPAエンティティに付けました。  
  
# 失敗の記録  
  
## 自前のRepositoryファクトリを使うようにする  
  
Configurationに以下のようなアノテーションをつけて、自前のRepositoryファクトリを使うようにしました。  
  
```
@EnableJpaRepositories(basePackages = "com.example.dataaccess.repositories",
        repositoryFactoryBeanClass = PrimaryRepositoryFactoryBean.class)
```  
  
## 作成したRepositoryファクトリ  
  
  
**PrimaryRepositoryFactoryBean.class**  
```java:PrimaryRepositoryFactoryBean.class
public class PrimaryRepositoryFactoryBean<T extends Repository<S, ID>, S, ID extends Serializable> extends JpaRepositoryFactoryBean<T, S, ID> {

    @Autowired // この子がSystemIdを持っている
    private CurrentAppUserDetailsProvider currentAppUserDetailsProvider;

    @Override // RepositoryFactoryを返します
    protected RepositoryFactorySupport createRepositoryFactory(EntityManager entityManager) {
        return new MyRepositoryFactory(entityManager, currentAppUserDetailsProvider);
    }

    private static class MyRepositoryFactory<E, I extends Serializable> extends JpaRepositoryFactory {

        private final EntityManager entityManager;
        private final CurrentAppUserDetailsProvider currentAppUserDetailsProvider;

        public MyRepositoryFactory(EntityManager entityManager,
                CurrentAppUserDetailsProvider currentAppUserDetailsProvider) {
            super(entityManager);
            this.entityManager = entityManager;
            this.currentAppUserDetailsProvider = currentAppUserDetailsProvider;
        }

        @Override
        protected Object getTargetRepository(RepositoryMetadata metadata) {
            Object repository = super.getTargetRepository(metadata);

            SessionFactory sessionFactory = entityManager.getEntityManagerFactory().unwrap(SessionFactory.class);

            Session session = sessionFactory.getCurrentSession(); // 例外発生！！

            session.enableFilter("SYSTEM_ID_FILTER")
                    .setParameter("systemId", getSystemId());

            return repository;
        }

        private int getSystemId() {
            return currentAppUserDetailsProvider
                    .getCurrentAppUserDetails()
                    .getSystemId();
        }

    }
}
```  
  
# 失敗の内容  
  
Repositoryをインスタンス化する時点では、トランザクション始まってないからSession無いよーーー。  
  
当たり前だよねーーー。  
  
