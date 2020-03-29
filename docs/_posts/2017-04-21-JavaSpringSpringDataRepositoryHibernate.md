---
'created_at: ': '2017-04-21T00:26:41+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Java
      versions: []
    - name: spring
      versions: []
    - name: spring-boot
      versions: []
    - name: spring-data-jpa
      versions: []
  state: 0
title: "[Java][Spring] Spring Data\u306ERepository\u3092\u30A4\u30F3\u30B9\u30BF\u30F3\
  \u30B9\u5316\u3059\u308B\u969B\u306BHibernate\u306E\u30D5\u30A3\u30EB\u30BF\u3092\
  \u6709\u52B9\u306B\u3057\u3088\u3046\u3068\u3057\u3066\u5931\u6557\u3057\u305F\u8A18\
  \u9332"
'updated_at: ': '2017-04-21T00:28:29+09:00'

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
  
