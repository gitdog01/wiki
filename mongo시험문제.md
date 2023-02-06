# db 공부

## MONGODB OVERVIEW AND THE DOCUMENT MODEL

1. Identify the set of value types MongoDB BSON supports.
    - BSON 바이너리로 인코딩된 JSON 입니다.
    - 다음과 같은 타입을 가지게 됩니다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/abdcb77d-0295-4cfa-a37a-647bd3b93a17/Untitled.png)
    
2. Given three documents that are of dierent shape, identify which can co-exist in thesame collection.
    - ObjectId ( _id )에 대한 설명을 조금 해보려고 한다.
        - **ObjectId는 12바이트 스토리지**를 사용하며 24자리 16진수 문자열 표현이 가능하다. 바이트당 2자리를 사용한다. 여러개의 새로운 ObjectId를 연속으로 생성하면 매번 마지막 숫자 몇개만 바뀐다. 몇 초 간격을 두고 생성하면 중간 숫자 몇개가 바뀐다. 이는 생성되는 방식 때문이다.
        - **ObectId의 첫 4바이트는 타임스탬프다**. 1970년 1월 1일부터의 시간을 1/1000초 단위로 저장하는 타임스탬프이며 유용한 속성을 제공한다.
            - 타임스탬프는 4바이트 이후 뒤의 값 5바이트~ 묶일 때 초단위의 유일성을 제공한다.
            - 타임스탬프가 맨 처음에 온다는 것은 ObjectId가 대략 입력 순서대로 정렬된다는 의미다. 이는 확실히 보장되지는 않지만 효율적으로 인   덱싱하는 특성이 있다.
        - **다음 5바이트는 랜덤 값이다.** 최종 3바이트는 서로 다른 시스템에서 충돌하는 ObjectId들을 생성하지 않도록 랜덤 값으로 시작하는 카운터다. ObjectId의 앞 9바이트는 1초 동안 여러 장비와 프로세스에 걸쳐 **유일성을 보장**한다.
        - **마지막 3바이트는 단순히 증분하는 숫자**로 1초 내 단일 프로세스의 유일성을 보장한다. 고유한 ObjectId는 프로세스당 1초에 256^3(1677만7216)개까지 생성된다.
        - 필수적이며 고유값입니다.

## CRUD

1. Given a scenario with a type of structured document that needs to be inserted into a database, identify properly and improperly formed insert commands.
    - document insert
    - 해당 메소드들은 insert 처럼 쓸 수 있습니다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a2194e09-efab-4d3c-8a7f-1df73859aef2/Untitled.png)
    
2. Given an update scenario where an entire updated document (no update operatorsused) is provided, identify the output and how the database changed state.
    - 
3. Given an update scenario where $set is used, identify the output and how thedatabase changed state.
4. Given a scenario about updating a document and information about where it shouldbe inserted if it does not exist, identify the upsert command that should be used.
5. Given a scenario where multiple documents need to be updated, identify the correctupdate expression.
6. Given a findAndModify scenario where another operation is run concurrently, identifythe output and how the database changed state.
7. Given a scenario where a document should be deleted from the database, identify thedelete expression that should be used.
8. Given a scenario where a single document should be looked up by a simple equalityconstraint (eg {x: 3}), identify the expression that should be used.
9. Identify documents matched by a query with an equality constraint on an array field.
10. Identify documents matched by an expression with relational operators in it.
11. Identify documents matched by an expression with $in.
    - `{ field: { $in: [<value1>, <value2>, ... <valueN> ] } }`
    - array 로 주어진 특정 값에 대해서 찾습니다.
12. Identify documents matched by an $elemMatch expression.
13. Identify documents matched by an expression that has several logical operators.
14. Given a query with a sort and limit, identify the correct output.
15. Identify the incorrect projection among a set of expressions.
16. Identify how to get all results from a cursor.
17. Identify the expressions used to count the number of documents matching a query.
18. Given an indexing scenario, identify the correct command for defining a search index.
19. Given a scenario, identify the correct search query.
20. Given an aggregation expression using $match, $group, identify the correct output.
21. Given an aggregation expression using $lookup, identify the correct output.
22. Given an aggregation expression using $out, identify the correct output.

## INDEXES

1. Given a query that is performing a collection scan, identify which index wouldimprove the performance of this query.
2. Given a query that is performing a collection scan on an equality match on an arrayfield, identify which index would improve the performance of this query.
3. Given a query with no constraint and a sort of two fields that is doing collection scan,identify which index would improve the performance of this query.
4. Given a collection, identify how many indexes exist for that collection.
5. Identify poor production practices.
6. Identify the explain plan outputs that signify a potential performance issue,specifically whether an index is present or not for the given query.

## DATA MODELING

1. Given a scenario with three collections (a parent and two children) and the user,identify the embedded relationships and which should be linked.
2. Identify data model examples that are considered an anti-pattern.

## TOOLS AND TOOLING

1. Given a scenario to load Atlas Sample Dataset and then use Data Explorer to use it tofind a given first document in a collection

## DRIVERS ( Node )

1. Define what the XX driver is?
2. Define how the XX application connects/uses the XXX driver?
3. Define the components of the URI string used by MongoClient to connect the driver tothe database.
4. Identify what connection pooling is in terms of the driver and what advantages itoers.
5. Identify the correct syntax for the XX driver to insert one document and to insertmany documents.
6. Identify the correct syntax for the XX driver to update one document and to updatemany documents.
7. Identify the correct syntax for the XX driver to delete one document and to deletemany documents.
8. Identify the correct syntax for the XX driver to find many documents and to find onedocument.
9. Identify the correct syntax for the XX driver to create an aggregation pipeline.
10. Identify the dierent syntax for the XX driver when using the MongoDB QueryLanguage (MQL) and when using the Aggregation Framework.
