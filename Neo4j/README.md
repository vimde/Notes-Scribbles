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

## Exercise 2:
### 2.1. Retrieve all Movie nodes that have a _released_ property value of 2003
```
MATCH (m:Movie {released:2003}) RETURN m
```
### 2.2. Query the database for all property keys
```
CALL db.propertyKeys
```
### 2.3. Retrieve all Movies released in _2006_, returning their titles
```
MATCH (m:Movie {released:2006}) RETURN m.title
```
### 2.4. Retrieve all Movie nodes from the database and return the _title_, _released_, and _tagline_ values
```
MATCH (m:Movie) RETURN m.title, m.released, m.tagline
```
### 2.5. Modify the query in _2.4._ so that the headings for the columns of the table returned are more descriptive
```
MATCH (m:Movie) RETURN m.title AS `Movie title`, m.released AS Year, m.tagline AS Tagline
```

## Exercise 3:
### 3.1. Display the schema of the database
```
CALL db.schema
```
### 3.2. Retrieve all people who wrote the movie _Speed Racer_
```
MATCH (p:Person)-[:WROTE]->(:Movie {title:'Speed Racer'}) RETURN p.name //Movie node is an anonymous node
```
### Retrieve all people who have written movies
```
MATCH (p:Person)-[:WROTE]->(:Movie) RETURN p.name
```
### Retrieve people who have acted in a particular movie
```
MATCH (p:Person)-[:ACTED_IN]->(:Movie {title:'The Matrix'}) RETURN p
```
### Retrieve people who have directed a particular movie
```
MATCH (p:Person)-[:DIRECTED]->(:Movie {title:'The Matrix'}) RETURN p
```
### 3.3. Retrieve all movies that are connected to the person, _Tom Hanks_
```
MATCH (m:Movie)<--(:Person {name:'Tom Hanks'}) RETURN m
```
### Retrieve all people connected with a particular movie
```
MATCH (p:Person)-->(:Movie {name:'The Matrix'}) RETURN p
```
### 3.4. Retrieve information about the relationships Tom Hanks has with the set of movies retrieved earlier in _3.3_
```
MATCH (m:Movie)<-[rel]-(:Person {name:'Tom Hanks'}) RETURN m.title, type(rel)
```
### 3.5. Retrieve information about the roles that _Tom Hanks_ acted in
```
MATCH (:Person {name:'Tom Hanks'})-[rel:ACTED_IN]->(m:Movie) RETURN m.title, rel.roles
```
### Retrieve all roles played for a particular movie
```
MATCH (:Person)-[rel:ACTED_IN]-(m:Movie {title: 'The Matrix'}) RETURN rel.roles
```

## Exercise 4:
### 4.1. Retrieve all movies that Tom Cruise acted in and return their titles
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie) WHERE p.name = 'Tom Cruise' RETURN m.title
```
### 4.2. Retrieve all actors that were born in the 70’s and return their names and year born
```
MATCH (p:Person) WHERE 1970 <= p.born < 1980 RETURN p.name AS Name, p.born AS `Year Born`
```
### 4.3. Retrieve the actors who acted in the movie The Matrix who were born after 1960, and return their names and year born
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie) 
WHERE m.title = 'The Matrix' AND p.born > 1960 
RETURN p.name AS Name, p.born AS `Year born`
```
### 4.4. Retrieve all movies released in 2000 by testing the node label and the released property, returning the movie titles
```
MATCH (m) WHERE m:Movie AND m.released = 2000 RETURN m.title AS `Movie Name`
```
### 4.5. Retrieve all people that wrote movies by testing the relationship between two nodes, returning the names of the people and the titles of the movies
```
MATCH (p)-[rel]->(m) 
WHERE p:Person AND type(rel) = 'WROTE' AND m:Movie 
RETURN p.name AS People, m.title AS `Movie name`
```
### 4.6. Retrieve all people in the graph that do not have a born property, returning their names
```
MATCH (p:Person) WHERE NOT EXISTS (p.born) RETURN p.name AS People
```
### 4.7. Retrieve all people related to movies where the relationship has the rating property, then return their name, movie title, and the rating
```
MATCH (p:Person)-[rel]->(m:Movie) WHERE EXISTS(rel.rating) 
RETURN p.name AS People, m.title AS `Movie Name`, rel.rating AS Rating
```
### 4.8. Retrieve all actors whose name begins with James, returning their names
```
MATCH (p:Person)-[:ACTED_IN]->(:Movie) WHERE p.name STARTS WITH 'James' RETURN p.name AS People
```
### 4.9. Retrieve all REVIEW relationships from the graph where the summary of the review contains the string fun, returning the movie title reviewed and the rating and summary of the relationship
```
MATCH (:Person)-[rel:REVIEWED]->(m:Movie) 
WHERE toLower(rel.summary) CONTAINS 'fun' 
RETURN m.title AS Title, rel.rating AS Rating, rel.summary AS Summary
```
### Retrieve all movies in the database that have love in their tagline and return the movie titles
```
MATCH (m:Movie) WHERE toLower(m.tagline) CONTAINS 'love' RETURN m.title AS Title
```
### Retrieve movies in the database, specifying a regular expression for the content of the tagline
```
MATCH (m:Movie) WHERE m.tagline =~ '(?i).*love.*' RETURN m.title AS Title
```
### 4.10. Retrieve all people who have produced a movie, but have not directed a movie, returning their names and the movie titles
```
MATCH (p:Person)-[:PRODUCED]->(m:Movie) 
WHERE NOT ((p)-[:DIRECTED]->(:Movie))
RETURN p.name AS People, m.title AS Title
```
### 4.11. Retrieve the movies and their actors where one of the actors also directed the movie, returning the actors names, the director’s name, and the movie title
```
MATCH (p1:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(p2:Person)
WHERE exists( (p2)-[:DIRECTED]->(m) )
RETURN  p1.name as Actor, p2.name as `Actor/Director`, m.title as Movie
```
### 4.12. Retrieve all movies that were released in the years 2000, 2004, and 2008, returning their titles and release years
```
MATCH (m:Movie) WHERE m.released IN [2000, 2004, 2008] 
RETURN m.title AS Title, m.released AS `Released Year`
```
### 4.13. Retrieve the movies that have an actor’s role that is the name of the movie, return the movie title and the role
```
MATCH (:Person)-[rel:ACTED_IN]->(m:Movie) WHERE m.title IN rel.roles
RETURN m.title AS Title, rel.roles AS Role
```

## Exercise 5:
### 5.1. Write a Cypher query that retrieves all movies that Gene Hackman has acted it, along with the directors of the movies. In addition, retrieve the actors that acted in the same movies as Gene Hackman. Return the name of the movie, the name of the director, and the names of actors that worked with Gene Hackman
```
MATCH (gene:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(dir:Person),
(others:Person)-[:ACTED_IN]->(m)
WHERE gene.name = 'Gene Hackman'
RETURN m.title AS `Movie name`, dir.name AS Director, others.name AS `Other Actors`
```
### 5.2. Retrieve all nodes that the person named James Thompson directly has the FOLLOWS relationship in either direction
```
MATCH (p:Person)-[:FOLLOWS]-(oth:Person) 
WHERE p.name = 'James Thompson'
RETURN p, oth
```
### 5.3. Modify the query in 5.2. to retrieve nodes that are exactly three hops away
```
MATCH (p:Person)-[:FOLLOWS * 3]-(oth:Person)
WHERE p.name = 'James Thompson'
RETURN p, oth
```
### 5.4. Modify the query to retrieve nodes that are one and two hops away
```
MATCH (p:Person)-[:FOLLOWS * 1..2]-(oth:Person)
WHERE p.name = 'James Thompson'
RETURN p, oth
```
### 5.5. Modify the query to retrieve all nodes that are connected to James Thompson by a Follows relationship no matter how many hops are required
```
MATCH (p:Person)-[:FOLLOWS*]-(oth:Person)
WHERE p.name = 'James Thompson'
RETURN p, oth
```
### 5.6. Write a Cypher query to retrieve all people in the graph whose name begins with Tom and optionally retrieve all people named Tom who directed a movie
```
MATCH (tom:Person)
WHERE tom.name STARTS WITH 'Tom'
OPTIONAL MATCH (tom)-[:DIRECTED]-(:Movie)
RETURN tom
```
### 5.7. Retrieve actors and the movies they have acted in, returning each actor’s name and the list of movies they acted in
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
RETURN collect(m.title) AS Movies, p.name AS Actor
```
### 5.8. Retrieve all movies that Tom Cruise has acted in and the co-actors that acted in the same movie, returning the movie title and the list of co-actors that Tom Cruise worked with
```
MATCH (tom:Person)-[:ACTED_IN]-(m:Movie)<-[:ACTED_IN]-(coactors:Person)
WHERE tom.name = 'Tom Cruise'
RETURN m.title AS Movie, collect(coactors.name) AS CoActors
```
### 5.9. Retrieve all people who reviewed a movie, returning the list of reviewers and how many reviewers reviewed the movie
```
MATCH (p:Person)-[:REVIEWED]->(m:Movie)
RETURN m.title AS Movie, collect(p.name) AS Reviewers, count(p) AS `Number of reviewers`
```
### 5.10. Retrieve all directors, their movies, and people who acted in the movies, returning the name of the director, the number of actors the director has worked with, and the list of actors
```
MATCH (dir:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(actors:Person)
RETURN dir.name AS Director, count(actors) AS `Number of Actors`, collect(actors.name) AS `Actors List`
```
### 5.11. Retrieve the actors who have acted in exactly five movies, returning the name of the actor, and the list of movies for that actor
```
MATCH (actor:Person)-[:ACTED_IN]-(m:Movie)
WITH actor, count(m) AS numOfMovies, collect(m.title) AS `Movies List`
WHERE numOfMovies = 5
RETURN actor.name, `Movies List`
```
### 5.12. Retrieve the movies that have at least 2 directors, and optionally the names of people who reviewed the movies
```
MATCH (m:Movie)
WITH m, size((:Person)-[:DIRECTED]->(m)) AS numOfDirectors
WHERE numOfDirectors >= 2
OPTIONAL MATCH (reviewer:Person)-[:REVIEWED]->(m)
RETURN m.title AS Movie, reviewer.name
```
