--Table Creation

CREATE TABLE Movies
(Title varchar(50) not null,
Year smallint not null,
director varchar(50),
Country varchar(50),
Rating decimal(2,1),
Genre varchar(50),
Gross decimal (12,0),
Producer varchar(50),
PRIMARY KEY (Title, Year)
);

CREATE TABLE Actors
(Title varchar(50) not null,
Year smallint not null,
Character_name varchar(50) not null,
Actor varchar(50) not null,
PRIMARY KEY (Title, Year, Character_name),
FOREIGN KEY (Title, Year) REFERENCES Movies (Title, Year)
);

CREATE TABLE Awards
(Title varchar(50) not null,
Year smallint not null,
Award varchar(50) not null,
Result varchar(50) not null,
PRIMARY KEY (Title, Year, Award),
FOREIGN KEY (Title, Year) REFERENCES Movies (Title, Year)
);

--Table Population


INSERT INTO Movies
Values ('The Girl With The Dragon Tattoo', 2011, 'David Flincher', 'USA', 7.9, 'Crime', 102515739, 'Scott Rudin');

INSERT INTO Movies
Values ('Casino Royale', 2006, 'Martin Campbell', 'UK', 7.9, 'Action', 594239066, 'Michael G. Wilson');

INSERT INTO Movies
Values ('Black Swan', 2010, 'Darren Aronofsky', 'USA', 8.1, 'Psychologic Thriller/Horror', 106952327, 'Scott Franklin');

INSERT INTO Movies
Values ('The Kings Speech', 2010, 'Tom Hooper', 'UK', 8.2, 'Historical Drama', 373700000, 'Iain Canning');

INSERT INTO Movies
Values ('Inception', 2010, 'Christopher Nolan', 'USA', 8.8, 'Thriller', 825532764, 'Christopher Nolan');

INSERT INTO Movies
Values ('Titanic', 1997, 'James Cameron', 'USA', 7.6, 'Romance', 2185372302, 'James Cameron');

INSERT INTO Awards
Values ('Titanic', 1997, 'Oscar, Director', 'won');

INSERT INTO Awards
Values ('Titanic', 1997, 'Oscar, Dramatic Score', 'won');

INSERT INTO Awards
Values ('Titanic', 1997, 'Oscar, Best Picture', 'won');

INSERT INTO Awards
Values ('Titanic', 1997, 'Oscar, Best Sound', 'won');

INSERT INTO Awards
Values ('Titanic', 1997, 'Oscar, Best Actress', 'nominated');

INSERT INTO Awards
Values ('Black Swan', 2010, 'Oscar, Best Actress', 'won');

INSERT INTO Awards
Values ('Black Swan', 2010, 'Oscar, Best Picture', 'nominated');

INSERT INTO Awards
Values ('Black Swan', 2010, 'Oscar, Best Directing', 'nominated');

INSERT INTO Awards
Values ('Inception', 2010, 'Oscar, Best Cinematography', 'won');

INSERT INTO Awards
Values ('Inception', 2010, 'Oscar, Best Visual Effects', 'won');

INSERT INTO Awards
Values ('Inception', 2010, 'Oscar, Best Picture', 'nominated');

INSERT INTO Awards
Values ('The Kings Speech', 2010, 'Oscar, Best Picture', 'won');

INSERT INTO Awards
Values ('The Kings Speech', 2010, 'Oscar, Best Actor', 'won');

INSERT INTO Awards
Values ('The Kings Speech', 2010, 'Oscar, Best Cinematography', 'nominated');

INSERT INTO Awards
Values ('Casino Royale', 2006, 'Bafta, Best Sound', 'won');

INSERT INTO Awards
Values ('Casino Royale', 2006, 'Bafta, Best Actor', 'nominated');

INSERT INTO Awards
Values ('The Girl With The Dragon Tattoo', 2011, 'Oscar, Best Film Editing', 'won');

INSERT INTO Awards
Values ('The Girl With The Dragon Tattoo', 2011, 'Oscar, Best Actress', 'nominated');

INSERT INTO Actors
Values ('The Girl With The Dragon Tattoo', 2011, 'Mikael Blomkvist', 'Daniel Craig');

INSERT INTO Actors
Values ('The Girl With The Dragon Tattoo', 2011, 'Lisbeth Salander', 'Rooney Mara');

INSERT INTO Actors
Values ('Casino Royale', 2006, 'James Bond', 'Daniel Craig');

INSERT INTO Actors
Values ('Casino Royale', 2006, 'Vesper Lynd', 'Eva Green');

INSERT INTO Actors
Values ('The Kings Speech', 2010, 'King George VI', 'Colin Firth');

INSERT INTO Actors
Values ('The Kings Speech', 2010, 'Lionel Logue', 'Geoffrey Rush');

INSERT INTO Actors
Values ('Inception', 2010, 'Cobb', 'Leonardo DiCaprio');

INSERT INTO Actors
Values ('Inception', 2010, 'Ariadne', 'Ellen Page');

INSERT INTO Actors
Values ('Black Swan', 2010, 'Nina Sayers', 'Natalie Portman');

INSERT INTO Actors
Values ('Black Swan', 2010, 'Lily', 'Mila Kunis');

INSERT INTO Actors
Values ('Titanic', 1997, 'Jack Dawson', 'Leonardo DiCaprio');

INSERT INTO Actors
Values ('Titanic', 1997, 'Rose DeWitt Bukater', 'Kate Winslet');

--Queries

-- Query 1 -- List actors who played more than one character in the same movie

SELECT DISTINCT a1.actor
FROM actors a1, actors a2
Where (a1.title = a2.title) AND (a1.character_name <> a2.character_name) AND (a1.actor=a2.actor)

-- Query 2 -- Find the average gross for movies directed by a director who won an Oscar

Select AVG(gross) AS AvgGross
FROM movies m, (SELECT Distinct director
				FROM movies m, awards a
				Where (a.award LIKE '%Oscar%') AND (a.title = m.title)) a
Where m.director = a.director

-- Query 3 -- List producers, and the total amount of money their movies made.

SELECT producer, SUM(gross)
FROM movies
GROUP BY producer

-- Query 4 -- List producers who produced at least two movies that grossed more than $50 million in a single year.

SELECT DISTINCT a.producer
FROM movies a, movies b
WHERE a.producer = b.producer AND a.gross > 50000000 AND b.gross > 50000000 AND a.year = b.year AND a.title <> b.title

-- Query 5 -- For each director, select his/her movies rated higher than 8, ordered by their gross.
-- Wasn't sure whether gross was to be ordered Ascending or Descending, so I assumed Ascending

SELECT director, title, gross
FROM movies
WHERE rating > 8
Order By director, gross

-- Query 6 -- Find movies made in the 90s that were nominated for at least two different awards

SELECT DISTINCT m.title, m.year
from movies m, awards a, awards b
where m.year>1989 and m.year<2000 and m.title=a.title and m.title=b.title and a.award<>b.award

-- Query 7 -- Find movies made in the 90s that won every award they were nominated for.

SELECT DISTINCT m.title, m.year
FROM movies m, awards a
WHERE m.year>1989 and m.year<2000 and m.title=a.title 	and m.title <> ALL (SELECT DISTINCT (m2.title)
																					FROM movies m2, awards a2
																					WHERE m2.title = a2.title and a2.result='nominated')
														and m.year <> ALL (SELECT DISTINCT (m2.year)
																					FROM movies m2, awards a2
																					WHERE m2.title = a2.title and a2.result='nominated')

-- Query 8 -- List all comedies that won major Oscars (best film or director) before 1960 and after 1990.

SELECT m.title, m.year
FROM movies m, awards a
WHERE m.genre like '%Comedy%' and m.title = a.title and (a.award like '%Oscar%Best%Film%' or a.award like '%Oscar%Best%Director%')
and (m.year<1960 or m.year>1990) and a.result = 'won'

-- Query 9 -- Find directors who directed a comedy and a drama, with the comedy having a higher rating than the drama.

SELECT a.director
FROM movies a, movies b
WHERE a.genre like '%Comedy%' and b.genre like '%Drama%' and a.rating > b.rating and a.director = b.director

-- Query 10 -- Find actors who only act in high grossing (more than $50 million) movies.

SELECT DISTINCT a.actor 
FROM actors a, movies m
WHERE a.title = m.title
GROUP BY a.actor
HAVING MIN(m.gross) > 50000000

-- Query 11 -- For each award category, find the average rating of movies that won that award.

SELECT a.award, AVG(m.rating) as AvgRating
FROM awards a, movies m
WHERE a.result like '%won%' and m.title = a.title and m.year = a.year
GROUP BY a.award

-- Query 12 -- Find the award category whose winners have the highest average rating.

Select Distinct b.award
from (
			SELECT a.award, AVG(m.rating) as AvgRating
			FROM awards a, movies m
			WHERE a.result like '%won%' and m.title = a.title and m.year = a.year
			GROUP BY a.award
) b
where b.avgrating = (
Select max(c.avgrating)
from (
			SELECT a.award, AVG(m.rating) as AvgRating
			FROM awards a, movies m
			WHERE a.result like '%won%' and m.title = a.title and m.year = a.year
			GROUP BY a.award
) c
)

-- Query 13 -- Find all pairs of movies (m1,m2) nominated for the same award, such that m1 has higher rating than m2, but m2 won the award.

SELECT (m1.title, m2.title)
FROM movies m1, movies m2, awards a1, awards a2
WHERE a1.title = m1.title and a2.title=m2.title and a1.award = a2.award and m1.rating > m2.rating and a1.title <> a2.title and m1.year=m2.year and a2.result like '%won%'

-- Query 14 -- Find character names that appear in two movies produced in two different countries.

SELECT DISTINCT a1.character_name
FROM actors a1, actors a2, movies m1, movies m2
WHERE a1.character_name = a2.character_name and a1.title=m1.title and a2.title=m2.title and m1.country <> m2.country and a1.title <> a2.title
