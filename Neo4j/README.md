## Commands
- :help play
- :help keys - Keyboard shortcuts
- :help MATCH
- :sysinfo
- :history - history of commands
- :help server
- :clear

## Sample database
- :play movie graph
- MATCH (p:Person) RETURN p.name LIMIT 5
- MATCH n DETACH DELETE n;

# Exercises:
```
:play intro-neo4j-exercises
```
## Exercise 1:
### 1.1. Retrieve all nodes from the database
```
MATCH (n) RETURN n;
```
### 1.2. Examine the schema of your database
```
CALL db.schema
```
### 1.3. Retrieve all Person nodes
```
MATCH (p:Person) RETURN p;`
```
### 1.4. Retrieve all Movie nodes
```
MATCH (m:Movie) RETURN m;
```
