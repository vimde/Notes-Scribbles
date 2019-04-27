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

## Exercise 6
### 6.1. You want to know what actors acted in movies in the decade starting with the year 1990. First write a query to retrieve all actors that acted in movies during the 1990s, where you return the released date, the movie title, and the collected actor names for the movie. For now do not worry about duplication
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN m.released AS Released, m.title AS Movie, collect(p.name) AS Actors
```
### 6.2. The results returned from the previous query include multiple rows for a movie released value. Next, modify the query so that the released date records returned are not duplicated. To implement this, you must add the collection of the movie titles to the results returned
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN m.released AS Released, collect(m.title) AS `Movie Names`, collect(p.name) AS Actors
```
### 6.3. The results returned from the previous query returns the collection of movie titles with duplicates. That is because there are multiple actors per released year. Next, modify the query so that there is no duplication of the movies listed for a year
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN m.released AS Released, collect(DISTINCT m.title) AS `Movie Names`, collect(p.name) AS Actors
```
### 6.4. Modify the query that you just wrote to order the results returned so that the more recent years are displayed first
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN m.released AS Released, collect(DISTINCT m.title) AS `Movie Names`, collect(p.name) AS Actors
ORDER BY Released DESC
```
### 6.5. Retrieve the top 5 ratings and their associated movies, returning the movie title and the rating
```
MATCH (p:Person)-[rel:REVIEWED]->(m:Movie)
RETURN m.title AS Title, rel.rating AS Rating
ORDER BY Rating DESC LIMIT 5
```
### 6.6. Retrieve all actors that have not appeared in more than 3 movies. Return their names and list of movies
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, count(*) AS numOfMovies, collect(m.title) AS Movies
WHERE numOfMovies <= 3
RETURN p.name AS ACTOR, Movies
```

## Exercise 7
### 7.1. Write a Cypher query that retrieves all actors that acted in movies, and also retrieves the producers for those movies. During the query, collect the names of the actors and the names of the producers. Return the movie titles, along with the list of actors for each movie, and the list of producers for each movie making sure there is no duplication of data. Order the results returned based upon the size of the list of actors
```
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)<-[:PRODUCED]-(director:Person)
RETURN DISTINCT m.title AS Movie, collect(DISTINCT actor.name) AS Actors, 
collect(DISTINCT director.name) AS Directors
ORDER BY size(Actors)
```
### 7.2. Write a Cypher query that retrieves all actors that acted in movies, and collects the list of movies for any actor that acted in more than five movies. Return the name of the actor and the list
```
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)
WITH actor, collect(m.title) AS Movies
WHERE size(Movies) > 5
RETURN actor.name AS Actor, Movies
```
### 7.3. Modify the query you just wrote so that before the query processing ends, you unwind the list of movies and then return the name of the actor and the title of the associated movie
```
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)
WITH actor, collect(m) AS Movies
WHERE size(Movies) > 5
WITH actor, Movies UNWIND Movies AS movie
RETURN actor.name AS Actor, movie.title AS Title
```
### 7.4. Write a query that retrieves all movies that Tom Hanks acted in, returning the title of the movie, the year the movie was released, the number of years ago that the movie was released, and the age of Tom when the movie was released
```
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)
WHERE actor.name = 'Tom Hanks'
RETURN m.title AS Movie, m.released AS Year, date().year - m.released AS `Years Ago Released`, 
m.released - actor.born AS Age
```

## Exercise 8
### 8.1. Create a Movie node for the movie with the title, Forrest Gump
```
CREATE (:Movie {title: 'Forrest Gump'})
```
### 8.2. Retrieve the node you just created by its title
```
MATCH (m:Movie {title: 'Forrest Gump'}) RETURN m
```
### 8.3. Create a Person node for the person with the name, Robin Wright
```
CREATE (:Person {name: 'Robin Wright'})
```
### 8.4. Retrieve the Person node you just created by its name
```
MATCH (p:Person {name: 'Robin Wright'}) RETURN p
```
### 8.5. Add the label OlderMovie to any Movie node that was released before 2010
```
MATCH (m:Movie)
WHERE m.released < 2010
SET m:OlderMovie
RETURN DISTINCT labels(m)
```
### 8.6. Retrieve all older movie nodes to test that the label was indeed added to these nodes
```
MATCH (m:OlderMovie) RETURN m
```
### 8.7. Add the label Female to all Person nodes that has a person whose name starts with Robin
```
MATCH (p:Person) WHERE p.name STARTS WITH 'Robin'
SET p:Female
RETURN DISTINCT labels(p)
```
### 8.8. Retrieve all Female nodes
```
MATCH (f:Female) RETURN f
```
### 8.9. We’ve decided to not use the Female label. Remove the Female label from the nodes that have this label
```
MATCH (f:Female) REMOVE f:Female
```
### 8.10. View the current schema of the graph
```
CALL db.schema()
```
### 8.11. Add the following properties to the movie, Forrest Gump: released: 1994, tagline: Life is like a box of chocolates… you never know what you’re gonna get, lengthInMinutes: 142
```
MATCH (m:Movie) WHERE m.title = 'Forrest Gump' 
SET m += {released:1994, tagline:'Life is like a box of chocolates... You never know what you\'re gonna get', 
  lengthInMinutes:142}
RETURN m
```
### 8.12. Retrieve this OlderMovie node to confirm that the properties and label have been properly set
```
MATCH (om:OlderMovie) WHERE om.title = 'Forrest Gump' RETURN om
```
### 8.13. Add the following properties to the person, Robin Wright: born: 1966, birthPlace: Dallas
```
MATCH (p:Person) WHERE p.name = 'Robin Wright' 
SET p.born = 1966, p.birthPlace = 'Dallas'
RETURN p
```
### 8.14. Retrieve this Person node to confirm that the properties have been properly set
```
MATCH (p:Person) WHERE p.name = 'Robin Wright' RETURN p.name, p.born, p.birthPlace
```
### 8.15. Remove the lengthInMinutes property from the movie, Forrest Gump
```
MATCH (m:Movie) WHERE m.title = 'Forrest Gump' REMOVE m.lengthInMinutes
```
### 8.16. Retrieve the Forrest Gump node to confirm that the property has been removed
```
MATCH (m:Movie) WHERE m.title = 'Forrest Gump' RETURN m
```
### 8.17. Remove the birthPlace property from the person, Robin Wright (another way to remove the property)
```
MATCH (p:Person) WHERE p.name = 'Robin Wright' SET p.birthPlace = null
```
### 8.18. Retrieve the Robin Wright node to confirm that the property has been removed
```
MATCH (p:Person) WHERE p.name = 'Robin Wright' RETURN p
```
## Exercise 9
### 9.1. Create the ACTED_IN relationship between the actors, Robin Wright, Tom Hanks, and Gary Sinise and the movie, Forrest Gump
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
MATCH (p:Person)
WHERE p.name = 'Robin Wright' OR p.name = 'Tom Hanks' OR p.name = 'Gary Sinise'
CREATE (p)-[:ACTED_IN]->(m)
```
### 9.2. Create the DIRECTED relationship between Robert Zemeckis and the movie, Forrest Gump
```
MATCH (p:Person {name:'Robert Zemeckis'})
MATCH (m:Movie {title:'Forrest Gump'})
CREATE (p)-[:DIRECTED]->(m)
```
### 9.3. Create a new relationship, HELPED from Tom Hanks to Gary Sinise
```
MATCH (tom:Person {name:'Tom Hanks'})
MATCH (gary:Person {name:'Gary Sinise'})
CREATE (tom)-[:HELPED]->(gary)
```
### 9.4. Write a Cypher query to return all nodes connected to the movie, Forrest Gump, along with their relationships
```
MATCH (m:Movie)-[rel]-(p:Person) WHERE m.title = 'Forrest Gump'
RETURN m, rel, p
```
### 9.5. Add the roles property to the three ACTED_IN relationships that you just created to the movie, Forrest Gump using this information: Tom Hanks played the role, Forrest Gump. Robin Wright played the role, Jenny Curran. Gary Sinise played the role, Lieutenant Dan Taylor
```
MATCH (tom:Person {name:'Tom Hanks'})-[rel:ACTED_IN]->(m:Movie {title:'Forrest Gump'})
SET rel.roles = ['Forrest Gump']
MATCH (robin:Person {name:'Robin Wright'})-[rel:ACTED_IN]->(m2:Movie {title:'Forrest Gump'})
SET rel.roles = ['Jenny Curran']
MATCH (gary:Person {name:'Gary Sinise'})-[rel:ACTED_IN]->(m3:Movie {title:'Forrest Gump'})
SET rel.roles = ['Lieutenant Dan Taylor']

//Another query

MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
SET rel.roles =
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
  WHEN 'Gary Sinise' THEN ['Lieutenant Dan Taylor']
END
```
### 9.6. Add a new property, research to the HELPED relationship between Tom Hanks and Gary Sinise and set this property’s value to war history
```
MATCH (:Person {name:'Tom Hanks'})-[rel:HELPED]->(:Person {name:'Gary Sinise'})
SET rel.research = 'war history'
```
### 9.7. View the current list of property keys in the graph
```
CALL db.propertyKeys
```
### 9.8. View the current schema of the graph
```
CALL db.schema
```
### 9.9. Query the graph to return the names and roles of actors in the movie, Forrest Gump
```
MATCH (actor:Person)-[rel:ACTED_IN]->(m:Movie {title:'Forrest Gump'})
RETURN actor.name AS Actor, rel.roles AS Roles
```
### 9.10. Query the graph to retrieve information about any HELPED relationships
```
MATCH (p1:Person)-[rel:HELPED]->(p2:Person)
RETURN p1.name, rel, p2.name
```
### 9.11. Modify the role that Gary Sinise played in the movie, Forrest Gump from Lieutenant Dan Taylor to Lt. Dan Taylor
```
MATCH (gary:Person {name:'Gary Sinise'})-[rel:ACTED_IN]->(m:Movie {title:'Forrest Gump'})
SET rel.roles = ['Lt. Dan Taylor']
```
### 9.12. Remove the research property from the HELPED relationship from Tom Hanks to Gary Sinise
```
MATCH (tom:Person {name:'Tom Hanks')-[rel:HELPED]->(gary:Person {name:'Gary Sinise'})
REMOVE rel.research
```
### 9.13. Query the graph to confirm that your modifications were made to the graph
```
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m
```
## Exercise 10
### 10.1. Delete the HELPED relationship from the graph
```
MATCH (:Person)-[rel:HELPED]-(:Person) DELETE rel
```
### 10.2. Query the graph to confirm that the relationship no longer exists
```
MATCH (:Person)-[rel:HELPED]-(:Person) RETURN rel
```
### 10.3. Query the graph to display Forrest Gump and all of its relationships
```
MATCH (m:Movie)-[rel]-(p:Person) WHERE m.title = 'Forrest Gump'
RETURN m, rel, p
```
### 10.4. Try deleting the Forrest Gump node without detaching its relationships
```
MATCH (m:Movie) WHERE m.title = 'Forrest Gump' DELETE m //Results in error
```
### 10.5. Delete Forrest Gump, along with its relationships in the graph
```
MATCH (m:Movie {title:'Forrest Gump'}) DETACH DELETE m
```
### 10.6. Query the graph to confirm that the Forrest Gump node has been deleted
```
MATCH (m:Movie {title:'Forrest Gump'}) RETURN m
```
## Exercise 11
### 11.1. Use MERGE to create (ON CREATE) a node of type Movie with the title property, Forrest Gump. If created, set the released property to 1994
```
MERGE (m:Movie {title:'Forrest Gump'})
ON CREATE SET m.released = 1994
RETURN m
```
### 11.2. Use MERGE to update (ON MATCH) a node of type Movie with the title property, Forrest Gump. If found, set the tagline property to "Life is like a box of chocolates… you never know what you’re gonna get."
```
MERGE (m:Movie {title:'Forrest Gump'})
ON MATCH SET m.tagline = 'Life is like a box of chocolates… you never know what you’re gonna get.'
RETURN m
```
### 11.3. Use MERGE to create (ON CREATE) a node of type Production with the title property, Forrest Gump. If created, set the property year to the value 1994
```
MERGE (p:Production {title:'Forrest Gump'})
ON CREATE SET p.year = 1994
RETURN p
```
### 11.4. Query the graph to find labels for nodes with the title property, Forrest Gump
```
MATCH (m) WHERE m.title = 'Forrest Gump' RETURN labels(m)
```
### 11.5. Use MERGE to update (ON MATCH) the existing Production node for Forrest Gump to add the company property with a value of Paramount Pictures
```
MERGE (p:Production {title:'Forrest Gump'})
ON MATCH SET p.company = 'Paramount Pictures'
RETURN p
```
### 11.6. Use MERGE to add the OlderMovie label to the movie, Forrest Gump
```
MERGE (m:Movie {title:'Forrest Gump'})
ON MATCH SET m:OlderMovie
RETURN m
```
### 11.7. Execute the following Cypher statement that uses MERGE to create two nodes and a single relationship
```
MERGE (p:Person {name: 'Robert Zemeckis'})-[:DIRECTED]->(m {title: 'Forrest Gump'})

This statement first finds all Person nodes that have only the name property value of Robert Zemeckis. 
It then finds all nodes with only the title property set to Forrest Gump. 
There are no Person or other nodes that have only these properties so the graph engine creates them. 
Then the graph engine creates the relationship between these two nodes. 
That is, this MERGE operation creates two nodes and a single relationship. 
If we had provided all of the property values for the nodes, we would not have created the extra nodes.

In fact, we should never create nodes and relationships together like this! 
This example is here to show you how powerful Cypher can be. 
A best practice is to create nodes first, then relationships.
```
### 11.8. Repeat the execution of the previous statement
```
MERGE (p:Person {name: 'Robert Zemeckis'})-[:DIRECTED]->(m {title: 'Forrest Gump'})

It should do nothing.
```
### 11.9. Find the correct Person node to delete
```
MATCH (p:Person {name: 'Robert Zemeckis'})-[rel]-(x)
WHERE NOT EXISTS (p.born)
RETURN p, rel, x
```
### 11.10. Delete this Person node, along with its relationships
```
MATCH (p:Person {name:'Robert Zemeckis'})-[rel]-(x)
WHERE NOT EXISTS (p.born)
DETACH DELETE p

OR

MATCH (p:Person {name: 'Robert Zemeckis'})--()
WHERE NOT EXISTS (p.born)
DETACH DELETE p
```
### 11.11. Find the correct Forrest Gump node (created in 11.8.) to delete by executing this statement:
```
MATCH (m)
WHERE m.title = 'Forrest Gump' AND labels(m) = []
RETURN m, labels(m)
```
### 11.12. Delete this Forrest Gump node
```
MATCH (m)
WHERE m.title = 'Forrest Gump' AND labels(m) = []
DETACH DELETE m
```
### 11.13. Use MERGE to create the DIRECTED relationship between Robert Zemeckis and the Movie, Forrest Gump
```
MATCH (p:Person), (m:Movie)
WHERE p.name = 'Robert Zemeckis' AND m.title = 'Forrest Gump'
MERGE (p)-[:DIRECTED]->(m)
```
### 11.14. Use MERGE to create the ACTED_IN relationship between the actors, Tom Hanks, Gary Sinise, and Robin Wright and the Movie, Forrest Gump
```
MATCH (p:Person), (m:Movie)
WHERE p.name IN ['Tom Hanks', 'Gary Sinise', 'Robin Wright'] AND m.title = 'Forrest Gump'
MERGE (p)-[:ACTED_IN]->(m)
```
### 11.15. Modify the relationship property, role for their roles in Forrest Gump: Tom Hanks is Forrest Gump, Gary Sinise is Lt. Dan Taylor, Robin Wright is Jenny Curran
```
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump' 
SET rel.roles = 
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Gary Sinise' THEN ['Lt. Dan Taylor']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
END
```
## Exercise 12
### 12.1. Write and execute a Cypher query that returns the names of people who reviewed movies and the actors in these movies by returning the name of the reviewer, the movie title reviewed, the release date of the movie, the rating given to the movie by the reviewer, and the list of actors for that particular movie
```
MATCH (p:Person)-[:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
RETURN DISTINCT p.name AS Reviewer, m.title AS Title, m.year AS `Release Data`, 
m.rating AS Rating, collect(a.name) AS Actors
```
### 12.2. Add a parameter named year to your session with a value of 2000
```
:param year => 2000
```
### 12.3. Modify the Cypher query you just wrote to filter by the year parameter
```
MATCH (p:Person)-[:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year
RETURN DISTINCT p.name AS Reviewer, m.title AS Title, m.year AS `Release Data`, 
m.rating AS Rating, collect(a.name) AS Actors
```
### 12.4. Modify the year parameter to be a different value, 2006, and retest your query
```
:param year => 2006
```
### 12.5. Add a parameter named ratingValue to your session with a value of 65
```
:params {year:2006, ratingValue:65}
```
### 12.6. Modify the query you wrote previously to also filter the result returned by the rating for the movie
```
MATCH (p:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND rel.rating > $ratingValue
RETURN DISTINCT p.name AS Reviewer, m.title AS Title, m.year AS Year, 
m.rating AS Rating, collect(a.name) AS Actors
```
### 12.7. Modify the ratingValue parameter to be a different value, 60, and retest your query
```
:param ratingValue => 60
```
## Exercise 13
### 13.1. View the query plan for this Cypher statement
```
MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)

Query Plan:
EXPLAIN MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)
```
### 13.2. View the metrics for the query when the previous statement executes
```
PROFILE MATCH (r:Person)-[rel:REVIEWED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)
```
### 13.3. Remove the labels from the nodes and relationships in the query and again view the metrics. Compare the db hits from the previous version of the statement
```
PROFILE MATCH (r)-[rel]->(m)<-[:ACTED_IN]-(a)
WHERE m.released = $year AND
      rel.rating > $ratingValue
RETURN  DISTINCT r.name, m.title, m.released, rel.rating, collect(a.name)
```
### 13.4. Execute this long-running query in your original Neo4j Browser session that returns a lot of results. In the second Neo4j Browser window, monitor the running queries
```
//A bad Cypher query
PROFILE MATCH (a)--(b)--(c)--(d)--(e)--(f)--(g)
RETURN a
//In the second browser window
:queries // available only in the Enterprise Edition of Neo4j
```
### 13.5. Execute this long-running query in your original Neo4j Browser session and monitor the query in the second Neo4j Browser session
```
//An even worse query than the one in 13.4
PROFILE MATCH (a), (b), (c), (d), (e) , (f), (g)
RETURN count(id(a))
```
### 13.6. Kill a query
```
:queries
//Double click on the icon to kill the long running query
```
## Exercise 14
### 14.1. Add a uniqueness constraint to the Person nodes in the graph, name should be unique
```
CREATE CONSTRAINT ON (p:Person) ASSERT (p.name) IS UNIQUE
```
### 14.2. Add Tom Hanks to the graph
```
CREATE (p:Person {name:'Tom Hanks'}) //This should result in an error because of the constraint added in 14.1.
```
### 14.3. Attempt to add an existence constraint to the Person nodes in the graph with the property born
```
CREATE CONSTRAINT ON (p:Person) ASSERT exists(p.born)
//Results in an error because there are 'Person' nodes without the property 'born'
```
### 14.4. Update the existing Person nodes so that you set the born property to 0 for any nodes that do not exist
```
MATCH (p:Person) WHERE NOT exists(p.born) SET p.born = 0
```
### 14.5. Add the existence constraint to the graph for the born property
```
CREATE CONSTRAINT ON (p:Person) ASSERT exists(p.born)
```
### 14.6. Add Sean Penn to the graph where you do not specify a value for born
```
CREATE (p:Person {name:'Sean Penn'}) RETURN p
```
### 14.7. Add an existence constraint to the ACTED_IN relationship in the graph for the roles property
```
CREATE CONSTRAINT ON ()-[rel:ACTED_IN]-() ASSERT exists(rel.roles)
```
### 14.8. Add an ACTED_IN relationship from the person, Emil Eifrem to the movie, Forrest Gump where the roles property is not set
```
MATCH (p:Person), (m:Movie)
WHERE p.name = 'Emil Eifrem' AND m.title = 'Forrest Gump'
MERGE (p)-[:ACTED_IN]->(m)
```
### 14.9. Add a node key to the graph that will ensure that the combined values of title and released are unique for all Movie nodes
```
CREATE CONSTRAINT ON (m:Movie) ASSERT (m.title, m.released) IS NODE KEY
```
### 14.10. Add the movie, Back to the Future with a released value of 1985 and a tagline value of Our future
```
CREATE (m:Movie {title:'Back to the Future', released:1985, tagline:'Our future'}) RETURN m
```
### 14.11. Add the movie, Back to the Future with a released value of 2018 and a tagline value of The future is ours
```
CREATE (m:Movie {title:'Back to the Future', released:2018, tagline:'The future is ours'})
```
### 14.12. Try adding the 2018 movie again
```
CREATE (m:Movie {title:'Back to the Future', released:2018, tagline:'The future is ours'}) 
//Will result in error
```
### 14.13. Display the list of constraints defined in the graph
```
CALL db.constraints()
```
### 14.14. Drop the constraint that requires the ACTED_IN relationship to have a property, roles
```
DROP CONSTRAINT ON ()-[rel:ACTED_IN]-() ASSERT exists(rel.roles)
```
