## cypher text to generate dataset on ne04j 
// Create users
UNWIND range(1, 100) AS userId
CREATE (:User {id: userId, name: 'User ' + userId});
// Generate transactions for high amounts coming to one account
MATCH (receiver:User {id: 39})
WITH receiver
UNWIND [100000, 200000, 300000] AS amount
CREATE (sender:User)-[:SENT_TO]->(:Transaction {amount: amount, timestamp: datetime({year: 2024, month: 1, day: 17, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(receiver);


// Generate transactions for different accounts sending money to one user
MATCH (receiver:User {id: 39})
WITH receiver
UNWIND [43, 58, 54, 29] AS senderId
CREATE (sender:User {id: senderId})-[:SENT_TO]->(:Transaction {amount: toInteger(rand()*5000), timestamp: datetime({year: 2024, month: 1, day: 17, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(receiver);

// Generate transactions for high amounts coming to one account
MATCH (receiver:User {id: 39})
WITH receiver
UNWIND [100000, 200000, 300000] AS amount
CREATE (sender:User)-[:SENT_TO]->(:Transaction {amount: amount, timestamp: datetime({year: 2024, month: 1, day: 17, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(receiver);

// Generate transactions for high amounts getting distributed into small amounts
MATCH (sender:User {id: 55})
WITH sender
UNWIND [100000, 200000, 300000] AS initialAmount
WITH sender, initialAmount
UNWIND [1, 2, 3] AS parts
CREATE (sender)-[:SENT_TO]->(:Transaction {amount: toInteger(initialAmount/3), timestamp: datetime({year: 2024, month: 1, day: 17, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(:User {id: toInteger(rand() % 100)});

// Generate transactions for untimely (late night) transactions
MATCH (sender:User)
WITH sender
UNWIND range(1, 3) AS times
CREATE (sender)-[:SENT_TO]->(:Transaction {amount: toInteger(rand()*5000), timestamp: datetime({year: 2024, month: 1, day: 17, hour: toInteger(rand()*6) + 1, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(:User {id: toInteger(rand() % 100)});


## text to determine relationships between nodes 
// Additional relationships for user sending money to different accounts
MATCH (sender:User {id: 1})
WITH sender
UNWIND [10, 20, 30] AS recipientId
CREATE (sender)-[:SENT_TO]->(:Transaction {amount: toInteger(rand()*5000), timestamp: datetime({year: 2024, month: 1, day: 18, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(:User {id: recipientId});

// Additional relationships for different accounts sending money to one user
MATCH (receiver:User {id: 39})
UNWIND [50, 60, 70] AS senderId
MERGE (sender:User {id: senderId})
CREATE (sender)-[:SENT_TO]->(:Transaction {
    amount: toInteger(rand()*3000),
    timestamp: datetime({year: 2024, month: 1, day: 18, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})
})-[:RECEIVED_FROM]->(receiver);

// Additional relationships for high amounts coming to one account
MATCH (receiver:User {id: 39})
UNWIND [150000, 250000, 350000] AS amount
MERGE (sender:User)-[:SENT_TO]->(:Transaction {
    amount: amount,
    timestamp: datetime({year: 2024, month: 1, day: 18, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})
})-[:RECEIVED_FROM]->(receiver);

// Additional relationships for high amounts getting distributed into small amounts
MATCH (sender:User {id: 55})
WITH sender
UNWIND [400000, 500000, 600000] AS initialAmount
WITH sender, initialAmount
UNWIND [4, 5, 6] AS parts
CREATE (sender)-[:SENT_TO]->(:Transaction {amount: toInteger(initialAmount/6), timestamp: datetime({year: 2024, month: 1, day: 18, hour: toInteger(rand()*6) + 18, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(:User {id: toInteger(rand() % 100)});

// Additional relationships for untimely (late night) transactions
MATCH (sender:User)
WITH sender
UNWIND range(1, 2) AS times
CREATE (sender)-[:SENT_TO]->(:Transaction {amount: toInteger(rand()*7000), timestamp: datetime({year: 2024, month: 1, day: 18, hour: toInteger(rand()*6) + 1, minute: toInteger(rand()*59), second: toInteger(rand()*59)})})-[:RECEIVED_FROM]->(:User {id: toInteger(rand() % 100)});

## visualization cypher 

### contains diffferent paramters on which the nodes and their relationships are defined 

MATCH (n) RETURN n;
MATCH ()-[:SENT_TO]->(transaction:Transaction)-[:RECEIVED_FROM]->() RETURN transaction;
MATCH (user:User)-[:RECEIVED_FROM]->(transaction:Transaction)
RETURN user.id AS UserId, SUM(transaction.amount) AS TotalBalance
ORDER BY TotalBalance DESC;
MATCH (user:User)-[:RECEIVED_FROM]->(transaction:Transaction)
RETURN user.id AS UserId, SUM(transaction.amount) AS TotalBalance
ORDER BY TotalBalance DESC;
MATCH ()-[:SENT_TO]->(transaction:Transaction)-[:RECEIVED_FROM]->()
RETURN transaction
ORDER BY transaction.amount DESC
LIMIT 1;
MATCH ()-[:SENT_TO]->(transaction:Transaction)-[:RECEIVED_FROM]->()
RETURN SUM(transaction.amount) AS TotalMoneyFlow;
MATCH path = (user:User {id: 39})-[:RECEIVED_FROM|SENT_TO*]-(other)
RETURN path;


## sample images from neo4j workspace 

visuals of money flow represented by nodes about how the money had travelled 
![visualisation (1)](https://github.com/pr8teen/RJPOLICE_HACK_429_Data_SWAT_7/assets/127277877/d92b4d27-0dd3-4a7a-8777-4ce6ab644eb8)

largest transaction visualisation 
![visualisation (5)](https://github.com/pr8teen/RJPOLICE_HACK_429_Data_SWAT_7/assets/127277877/3faca40c-e4a8-49a5-a4c8-d9c5df9bf0c0)

money flow relation
![visualisation (6)](https://github.com/pr8teen/RJPOLICE_HACK_429_Data_SWAT_7/assets/127277877/24f9b950-6405-403c-81ac-32443b8ff2b3)

nodes and transactions 
![visualisation (7)](https://github.com/pr8teen/RJPOLICE_HACK_429_Data_SWAT_7/assets/127277877/cd12d009-6649-4d90-b69d-6b4180082a0c)

