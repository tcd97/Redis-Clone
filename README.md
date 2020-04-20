 ![enter image description here](https://img.shields.io/badge/python-3.8-blue.svg)
# Redis-Clone
This is a prototype for Redis with some basic functionalities. Itan be used like any REST API. 
### Table of Contents
---
 <details>
   <summary>Click to expand!</summary>

   ##### Redis-Clone
   1. Usage
   2. Overview
   3. Functions Implemented
      * GET()
      * SET()
      * EXPIRE()
      * ZADD()
      * ZRANGE()
      * ZRANK()
      * DELETE()
      * ZREVRANK()
      * ZREVRANGE()
      * TTL()
      * PING()
   4. Questions
 </details>
 
### Usage
---
 To use the app the dependency mentioned in the requirements.txt must be satisfed. 
Once the dependency has been satisfied app.py can directly be executed via any CLI. 

    python3 app.py
The server will then be live on [http://localhost:5000/](http://localhost:5000/)

### **Overview**
---
This prototype is an attempt to give Redis like functionalities. 11 different Redis functions have been implemented in this prototype. The most difficult part in this prototype was to attain persistence. 
#### Persistence
---
This was a major problem that aroused during the development of the project. This problem is solved in a very Novel way in this prototype. 
This problem is solved by logging. Here is the algorithm that was used to solve this problem:

1. A local txt file is created that will log all the queries that can alter the database(Ex: SET, DELETE, EXPIRE). A snapshot of a log file is shown below. 

![enter image description here](https://github.com/tcd97/Redis-Clone/blob/master/src/test/log_file.png)

2. Once the server is stopped then the complete database is erased(State Lost). But when the server is live again then first all the queries present in the log are executed to gain the lost state. 

Thus persistence is achieved in this way. 
This technique is used by Redis and is called Logging. 
Another method called Snapshotting is also used by Redis on demand of the user. This is not implemented here due to lack of time. 
Logging has some disadvantages which is discussed in Questions section


### **Function Implemented**
---
### 1. GET()

##### How to use?

`GET key`

##### Time Complexity: O(1)

This function simply checks if the string value exist for the given key. If the key is not present "None" is returned. If the value at the key is not string(suppose sorted set is present at the key) then an error is returned.

---
### 2. SET()

##### How to use?

`SET key value [EX seconds|PX milliseconds] [NX|XX] [KEEPTTL]`

##### Time Complexity: O(1)

This function is used to insert value with given key. If key is already holding a value it is overriden. It can operate in all different modes like a redis SET. 

---
### 3. EXPIRE()

##### How to use?

`EXPIRE key seconds`

##### Time Complexity: O(1)

This can be used to add timeout on any key irrespective of the value it is holding. this is also used as a special mode in SET as discussed above. the algorithm used for expiring is as follows :-

 1. Whenever SET was used to insert any value in the DataBase any entry in 'expire' table was also created with the same key. The value to this was initialised as Inifinite.
 2. Now when we give an expiration time for any key, the value in this 'expire' table is set as current time + expiration time.
 3. Now when we call a GET method for any key, it is first checked whether the key has already expired by just comparing current time with expiration time of the key. if it is expired then it is deleted otherwise returned.

This technique is called lazy deletion.
Although this technique works well but there is an issue of increasing size of our Database. Because we are only deleting an expired when it called via GET. if any expired key is not called for a long time it may reside in our database thus increasing the size of our database. 
To limit the increasing size of the database what Redis does is it randomly selects keys and checks if it has expired. A similar random algorithm has been implemented to control the increasing size of the DataBase. this check only happens when the keys in our hash table > 100. 

---
### 4. ZADD()

##### How to use?

`ZADD key [NX|XX] [CH] [INCR] score member [score member ...]`

##### Time Complexity: O(log(N)) (N represents number of items present in our Sorted Set)

Adds all the specified members with the specified scores to the sorted set stored at key. It is possible to specify multiple score / member pairs. If a specified member is already a member of the sorted set, the score is updated and the element reinserted at the right position to ensure the correct ordering.

If key does not exist, a new sorted set with the specified members as sole members is created. If the key exists but does not hold a sorted set, an error is returned. 

---
### 5. ZRANGE()

##### How to use?

`ZRANGE key start stop [WITHSCORES]`

##### Time Complexity: O(log(N)+M)
		 N being the number of elements in the sorted set and M the number of elements returned.

Returns the specified range of elements in the sorted set stored at key. The elements are considered to be ordered from the lowest to the highest score. Lexicographical order is used for elements with equal score.

---
### 6. ZRANK()

##### How to use?

`ZRANK key member`

##### Time Complexity: O(log(N))

Returns the rank of member in the sorted set stored at key, with the scores ordered from low to high. The rank (or index) is 0-based, which means that the member with the lowest score has rank 0.

---
### 7. DELETE()

##### How to use?

`DEL key`

##### Time Complexity: O(1)

This functions deletes the given key. If it successfully deletes the key then '1' is returned otherwise '0'. 

---
### 8. ZREVRANK()

##### How to use?

`ZREVRANK key member`

##### Time Complexity: O(log(N))

Returns the rank of member in the sorted set stored at key, with the scores ordered from high to low. The rank (or index) is 0-based, which means that the member with the highest score has rank 0.

---
### 9. ZREVRANGE()

##### How to use?

`ZRANGE key start stop [WITHSCORES]`

##### Time Complexity: O(log(N)+M)
		 N being the number of elements in the sorted set
		 M the number of elements returned.

Returns the specified range of elements in the sorted set stored at key. The elements are considered to be ordered from the highest to the lowest score. Lexicographical order is used for elements with equal score.

---
### 10. TTL()

##### How to use?

`TTL key`

##### Time Complexity: O(1)

This function return the remaning time of any key. If the key does not exist or has infinite TTL then 'None' is returned.

---
### 11. PING()

##### How to use?

`Ping`

##### Time Complexity: O(1)

This function is useful for checking if server is alive. Returns PONG if server is active. 

---
