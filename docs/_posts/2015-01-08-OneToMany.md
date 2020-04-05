---
date: '2015-01-08T20:23:58+09:00'
layout: post
published: true
qiita_article_id: c8fb4277e39ca43a51de
tags:
- jpa
title: OneToManyで外部キーを保存するための試行錯誤の記録
updated: '2015-01-08T20:26:01+09:00'

---
# 環境 #  
  
* GlassFish 4.1  
* JDK 1.8  
  
# 課題 #  
  
こんなテーブルがあったとします（太字は主キーです）。  
  
> HUMAN(__ID__)  
> CELL(__CELL_ID__, __HUMAN_ID__)  
  
HUMAN対CELLは1:nの関係です。  
  
CELL.HUMAN_IDが外部キーとなっていてHUMAN.IDを参照しています。  
  
エンティティは2つになります。  
  
ここで、CELL.HUMAN_IDにHUMAN.IDの値を自動でぶっこみたいなぁというお話です。  
  
# 試行 #  
  
## 試行1 ##  
  
`@Id`アノテーションを複数つけて、`@IdClass`で複合主キーを指定します。  
  
**Human.java**  
```java:Human.java
@Entity
public class Human implements Serializable {
    private static final long serialVersionUID = 1L;
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "ID")
    private Integer id;

    @Id
    @OneToMany(mappedBy = "human", cascade = CascadeType.ALL)
    private List<Cell> cellList;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }
    
    public void addCell(Cell cell) {
        if (cell.getHuman() != this) {
            cell.setHuman(this);
        }
        if (cellList == null ){
            cellList = new ArrayList<>();
        }
        
        if (!cellList.contains(cell))  {
            cellList.add(cell);
        }
    }
    // 略 hashCodeやequals
}
```  
  
**Cell.java**  
```java:Cell.java
@Entity
@IdClass(CellPK.class)
public class Cell implements Serializable {
    private static final long serialVersionUID = 1L;
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "CELL_ID")
    private Integer id;

    @Id
    @ManyToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "HUMAN_ID", referencedColumnName = "ID")
    private Human human;
    // 略
}
```  
  
**CellPK.java**  
```java:CellPK.java
public class CellPK implements Serializable {
    private static final long serialVersionUID = 1L;
    private Integer id;
    private Human human;
    // 略
}
```  
  
### 結果 ###  
  
ここで実行すると、そもそもデプロイできないです。  
  
```
Severe:   Exception while invoking class org.glassfish.persistence.jpa.JPADeployer prepare method
Severe:   javax.persistence.PersistenceException: Exception [EclipseLink-28018] (Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd): org.eclipse.persistence.exceptions.EntityManagerSetupException
Exception Description: Predeployment of PersistenceUnit [jpa-composite-onetomany_war_1.0-SNAPSHOTPU] failed.
Internal Exception: Exception [EclipseLink-7332] (Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd): org.eclipse.persistence.exceptions.ValidationException
Exception Description: The derived composite primary key attribute [human] of type [jpa.composite.onetomany.Human] from [jpa.composite.onetomany.CellPK] should be of the same type as its parent id field from [jpa.composite.onetomany.Human]. That is, it should be of type [java.lang.Integer].
	at org.eclipse.persistence.internal.jpa.EntityManagerSetupImpl.createPredeployFailedPersistenceException(EntityManagerSetupImpl.java:1954)
	at org.eclipse.persistence.internal.jpa.EntityManagerSetupImpl.predeploy(EntityManagerSetupImpl.java:1945)

```  
  
型を合わせないといけないらしい。  
  
## 試行2 ##  
  
型をあわせて、`@JoinColumn`を`@PrimaryKeyJoinColumn`へ変更しました。`@JoinColumn`のままだと、更新できるフィールドが複数あるからと怒られます。  
  
  
**CellPK.java**  
```java:CellPK.java
public class CellPK implements Serializable {
    private static final long serialVersionUID = 1L;
    private Integer id;
    private Integer humanId;
    // 略
}
```  
  
**Cell.java**  
```java:Cell.java
@Entity
@IdClass(CellPK.class)
public class Cell implements Serializable {
    // 略
    @Id
    @Column(name = "HUMAN_ID")
    private Integer humanId;
    
    @ManyToOne(cascade = CascadeType.ALL)
    @PrimaryKeyJoinColumn(name = "HUMAN_ID", referencedColumnName = "ID")
    private Human human;
    // 略
}
```  
  
### 結果 ###  
  
CELL.HUMAN_IDがセットされないので、SQLエラーになりました。  
  
```
javax.persistence.PersistenceException: Exception [EclipseLink-4002] (Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd): org.eclipse.persistence.exceptions.DatabaseException
Internal Exception: com.mysql.jdbc.exceptions.jdbc4.MySQLIntegrityConstraintViolationException: Column 'HUMAN_ID' cannot be null
Error Code: 1048
Call: INSERT INTO CELL (HUMAN_ID, CELL_ID) VALUES (?, ?)
	bind => [2 parameters bound]
Query: InsertObjectQuery(jpa.composite.onetomany.Cell[ id=2 ])
```  
  
## 試行3 ##  
  
Cellエンティティのセッターの中で、humanIdをセットするように変えてみました。  
  
**Cell.java**  
```java:Cell.java
@Entity
@IdClass(CellPK.class)
public class Cell implements Serializable {
    // 略
    public void setHuman(Human human) {
        this.human = human;
        this.humanId = human.getId();
        human.addCell(this);
    }
    // 略
}
```  
  
### 結果 ###  
  
状況変わらず……。  
  
```
Exception [EclipseLink-4002] (Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd): org.eclipse.persistence.exceptions.DatabaseException
Internal Exception: com.mysql.jdbc.exceptions.jdbc4.MySQLIntegrityConstraintViolationException: Column 'HUMAN_ID' cannot be null
Error Code: 1048
Call: INSERT INTO CELL (HUMAN_ID, CELL_ID) VALUES (?, ?)
	bind => [2 parameters bound]
Query: InsertObjectQuery(jpa.composite.onetomany.Cell[ id=2 ])
```  
  
## 試行4 ##  
  
そもそも呼び方間違ってね？  
  
****  
```java:
        Human human = new Human();
        human.addCell(new Cell());
        human.addCell(new Cell());
        em.persist(human);        
```  
  
を、  
  
****  
```java:
        Human human = new Human();
        em.persist(human);   
        human.addCell(new Cell());
        human.addCell(new Cell());
```  
  
のように書き換えました。`em`は`EntityManager`です。  
  
### 結果 ###  
  
成功しました～～～（喜）  
  
`@OneToOne`なら関連テーブルもいっぺんにpersistできるけど、`@OneToMany`は先にpersistしないといけないみたい。  
  
# 結論 #  
  
OneToManyを保存するときは、先に親エンティティをpersistすべし。  
