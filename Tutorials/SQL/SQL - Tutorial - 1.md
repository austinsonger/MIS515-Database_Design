BASICS
------


Show the population of Germany


    SELECT population
    FROM world
    WHERE name = 'Germany'


Show the per capita gdp: (gdp/population) for each country where the area is over 5,000,000 km^2


    SELECT name country, gdp/population per_capita_gdp
    FROM world
    WHERE area > 5000000


 Show the name and continent where the area is less then 2000 and the gdp is more than 5000000000

    SELECT name country, continent
    FROM world
    WHERE area < 2000
    AND gdp > 5000000000

Show the name and the population for 'Denmark', 'Finland', 'Norway', 'Sweden'

    SELECT name country, population
    FROM world
    WHERE name IN ('Denmark', 'Finland', 'Norway', 'Sweden')

Show each country that begins with G

    SELECT name country
    FROM world
    WHERE name LIKE 'G%'

Show the area in 1000 square km. Show area/1000 instead of area

    SELECT name country, area/1000
    FROM world
    WHERE area BETWEEN 207600 AND 244820



SELECT - NOBEL
--------------

Change the query shown so that it displays Nobel prizes for 1950.

    SELECT yr, subject, winner
    FROM nobel
    WHERE yr = 1950

Show who won the 1962 prize for Literature.

    SELECT winner winner_1962
    FROM nobel
    WHERE yr = 1962
    AND subject = 'Literature'

Show the year and subject that won 'Albert Einstein' his prize.

    SELECT yr, subject
    FROM nobel
    WHERE winner = 'Albert Einstein'

Give the name of the 'Peace' winners since the year 2000, including 2000.

    SELECT winner winner_peace
    FROM nobel
    WHERE subject = 'Peace'
    AND yr >= 2000

Show all details (yr, subject, winner) of the Literature prize winners for 1980 to 1989 inclusive.

    SELECT *
    FROM nobel
    WHERE subject = 'Literature'
    AND yr BETWEEN 1980 AND 1989

Show all details of the presidential winners: ('Theodore Roosevelt', 'Woodrow Wilson', 'Jed Bartlet', 'Jimmy Carter')

    SELECT *
    FROM nobel
    WHERE winner IN ('Theodore Roosevelt', 'Woodrow Wilson', 'Jed Bartlet',
      'Jimmy Carter')

Show the winners with first name John

    SELECT winner
    FROM nobel
    WHERE winner LIKE 'John%'

In which years was the Physics prize awarded but no Chemistry prize. 

    SELECT DISTINCT p.yr
    FROM nobel p
    WHERE p.subject = 'Physics'
    AND p.yr NOT IN
    (
      SELECT c.yr
      FROM nobel c
      WHERE c.subject = 'Chemistry'
    )



SELECT WITHIN SELECT
--------------------


List each country name where the population is larger than 'Russia'.

    SELECT name country
    FROM world
    WHERE population >
    (
      SELECT population FROM world
      WHERE name='Russia'
    )

List the name and continent of countries in the continents containing 'Belize', 'Belgium'.

    SELECT a.name country, a.continent
    FROM world a
    WHERE a.continent IN
    (
      SELECT b.continent
      FROM world b
      WHERE b.name IN ('Belize', 'Belgium')
    )


Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.

    SELECT a.name country
    FROM world a
    WHERE a.continent = 'Europe'
    AND a.gdp/a.population >
    (
      SELECT b.gdp/b.population
      FROM world b
      WHERE b.name = 'United Kingdom'
    )


Which country has a population that is more than Canada but less than Poland? Show the name and the population.

    SELECT a.name country, a.population
    FROM world a
    WHERE a.population >
    (
      SELECT b.population
      FROM world b
      WHERE b.name = 'Canada'
    )
    AND a.population <
    (
      SELECT c.population
      FROM world c
      WHERE c.name = 'Poland'
    )


Which countries have a GDP greater than any country in Europe? [Give the
name only.]

    SELECT a.name country
    FROM world a
    WHERE a.gdp > ALL
    (
      SELECT b.gdp
      FROM world b
      WHERE b.continent = 'Europe'
    )


Find the largest country (by area) in each continent, show the continent, the name and the area:

    SELECT continent, name country, area
    FROM world x
    WHERE area >= ALL
    (
      SELECT area FROM world y
      WHERE y.continent=x.continent
      AND area>0
    )

Find each country that belongs to a continent where all populations are less than 25000000. Show name, continent and population.

    SELECT a.name country, a.continent, a.population
    FROM world a
    WHERE 25000000 > ALL
    (
      SELECT b.population
      FROM world b
      WHERE a.continent = b.continent
      AND b.population > 0
    )

Some countries have populations more than three times that of any of their neighbours (in the same continent). Give the countries and continents.

    SELECT a.name country, a.continent
    FROM world a
    WHERE a.population > ALL
    (
      SELECT b.population*3
      FROM world b
      WHERE a.continent = b.continent
      AND a.name <> b.name
    )


WHERE
-----

Observe the result of running a simple SQL command.

    SELECT name country, continent, population
    FROM world

Show the name for the countries that have a population of at least 200 million.

    SELECT name country
    FROM world
    WHERE population>200000000

Give the name and the per capita GDP for those countries with a population of at least 200 million.

    SELECT name country, gdp/population per_capita_gdp
    FROM world
    WHERE population > 200000000

Show the (name) and (population) in millions for the countries of 'South America' Divide the population by 1000000 to get population in millions.

    SELECT name country, population/1000000 pop_millions
    FROM world
    WHERE continent = 'South America'

Show the (name) and (population) for 'France', 'Germany', 'Italy'

    SELECT name country, population
    FROM world
    WHERE name IN ('France', 'Germany', 'Italy')

Identify the countries which have names including the word 'United'

    SELECT name country
    FROM world
    WHERE name LIKE '%United%'


 SUM AND COUNT
--------------

Show the total population of the world.

    SELECT SUM(population) pop_world
    FROM world

List all the continents - just once each.

    SELECT DISTINCT continent
    FROM world

Give the total GDP of Africa

    SELECT SUM(gdp) gdp_Africa
    FROM world
    WHERE continent = 'Africa'

How many countries have an area of at least 1000000

    SELECT COUNT(name) num_countries
    FROM world
    WHERE area >= 1000000

What is the total population of ('France','Germany','Spain')

    SELECT SUM(population) total_pop
    FROM world
    WHERE name IN ('France','Germany','Spain')

For each continent show the continent and number of countries.

    SELECT continent, COUNT(name) num_countries
    FROM world
    GROUP BY continent

For each continent show the continent and number of countries with populations of at least 10 million.

    SELECT continent, COUNT(name) num_countries
    FROM world
    WHERE population >= 10E6
    GROUP BY continent

List the continents with total populations of at least 100 million.

    SELECT continent
    FROM world
    GROUP BY continent
    HAVING SUM(population) >= 100E6


JOIN OPERATIONS
---------------

Show matchid and player name for all goals scored by Germany. (teamid = 'GER')

    SELECT matchid, player
    FROM goal
    WHERE teamid = 'GER'

Show id, stadium, team1, team2 for game 1012

    SELECT id,stadium,team1,team2
    FROM game
    WHERE id = 1012

Show the player, teamid and mdate and for every German goal.
(teamid='GER')

    SELECT player, teamid, mdate
    FROM game
    JOIN goal ON (game.id = goal.matchid)
    WHERE teamid = 'GER'

Show the team1, team2 and player for every goal scored by a player called
Mario (player LIKE 'Mario%')

    SELECT team1, team2, player
    FROM goal
    JOIN game ON (game.id = goal.matchid)
    WHERE player LIKE 'Mario%'

Show (player), (teamid), (coach), (gtime) for all goals scored in the
first 10 minutes (gtime<=10)

    SELECT player, teamid, coach, gtime
    FROM goal
    JOIN eteam ON (goal.teamid = eteam.id)
    WHERE gtime <= 10

List the the dates of the matches and the name of the team in which
'Fernando Santos' was the team1 coach.

    SELECT mdate, teamname
    FROM game
    JOIN eteam ON (game.team1 = eteam.id)
    WHERE coach = 'Fernando Santos'

List the player for every goal scored in a game where the staium was
'National Stadium, Warsaw'

    SELECT player
    FROM goal
    JOIN game ON (goal.matchid = game.id)
    WHERE stadium = 'National Stadium, Warsaw'

Show names of all players who scored a goal against Germany.
SELECT DISTINCT player

    FROM game
    JOIN goal ON goal.matchid = game.id
    WHERE (team1 = 'GER' OR team2 = 'GER')
    AND teamid <> 'GER'

Show teamname and the total number of goals scored.

    SELECT teamname, COUNT(player) goals_scored
    FROM eteam JOIN goal ON eteam.id = goal.teamid
    GROUP BY teamname

Show the stadium and the number of goals scored in each stadium.

    SELECT stadium, COUNT(player) goals_scored
    FROM game
    JOIN goal ON game.id = goal.matchid
    GROUP BY stadium

For every match involving 'POL', show the matchid, date and the number of
goals scored.

    SELECT matchid, mdate, COUNT(player) goals_scored
    FROM game
    JOIN goal ON goal.matchid = game.id
    WHERE (team1 = 'POL' OR team2 = 'POL')
    GROUP BY goal.matchid

For every match where 'GER' scored, show matchid, match date and the
number of goals scored by 'GER'

    SELECT matchid, mdate, COUNT(player)
    FROM game
    JOIN goal ON game.id = goal.matchid
    WHERE teamid = 'GER'
    GROUP BY game.id

List every match with the goals scored by each team as shown.
Sort your result by mdate, matchid, team1 and team2.

    SELECT mdate,
      team1,
      SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
      team2,
      SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
    FROM game JOIN goal ON goal.matchid = game.id
    GROUP BY game.id
    ORDER BY mdate, matchid, team1, team2


List the films where the yr is 1962 [Show id, title]

    SELECT id, title
    FROM movie
    WHERE yr=1962

Give year of 'Citizen Kane'.

    SELECT yr
    FROM movie
    WHERE title = 'Citizen Kane'

List all of the Star Trek movies, include the id, title and yr (all of
these movies include the words Star Trek in the title). Order results
by year.

    SELECT id, title, yr
    FROM  movie
    WHERE title LIKE '%Star Trek%'
    ORDER BY yr

What are the titles of the films with id 11768, 11955, 21191

    SELECT title
    FROM movie
    WHERE id IN (11768, 11955, 21191)

What id number does the actor 'Glenn Close' have?

    SELECT id
    FROM actor
    WHERE name = 'Glenn Close'

What is the id of the film 'Casablanca'

    SELECT id
    FROM movie
    WHERE title = 'Casablanca'

Obtain the cast list for 'Casablanca'. Use the id value that you obtained
in the previous question.

    SELECT name
    FROM actor
    JOIN casting ON actor.id = casting.actorid
    WHERE casting.movieid = 11768

Obtain the cast list for the film 'Alien'

    SELECT actor.name
    FROM actor
    JOIN casting ON actor.id = casting.actorid
    JOIN movie ON movie.id = casting.movieid
    WHERE title = 'Alien'

List the films in which 'Harrison Ford' has appeared

    SELECT movie.title
    FROM movie
    JOIN casting ON movie.id = casting.movieid
    JOIN actor ON actor.id = casting.actorid
    WHERE name = 'Harrison Ford'

List the films where 'Harrison Ford' has appeared - but not in the star
role. 

    SELECT movie.title
    FROM movie
    JOIN casting ON movie.id = casting.movieid
    JOIN actor ON actor.id = casting.actorid
    WHERE name = 'Harrison Ford'
    AND casting.ord <> 1

List the films together with the leading star for all 1962 films.

    SELECT title, name leading_star
    FROM movie
    JOIN casting ON movie.id = casting.movieid
    JOIN actor ON actor.id = casting.actorid
    WHERE movie.yr = 1962
    AND casting.ord = 1

Which were the busiest years for 'John Travolta', show the year and the
number of movies he made each year for any year in which he made at least
2 movies.

    SELECT yr, COUNT(title) num_movies
    FROM movie
    JOIN casting ON movie.id = casting.movieid
    JOIN actor ON casting.actorid = actor.id
    WHERE name = 'John Travolta'
    GROUP BY yr
    HAVING COUNT(title) =
    (
      SELECT MAX(c)
    	FROM
      (
    SELECT yr, COUNT(title) AS c
    FROM movie
    JOIN casting ON movie.id = casting.movieid
    JOIN actor ON casting.actorid = actor.id
    WHERE name = 'John Travolta'
    GROUP BY yr
      ) AS t
    )

List the film title and the leading actor for all of the films
'Julie Andrews' played in.

    SELECT DISTINCT b.title, a.name leading_star
    FROM
    (
      SELECT movie.title, actor.name, casting.movieid
      FROM movie
      JOIN casting ON movie.id = casting.movieid
      JOIN actor ON casting.actorid = actor.id
      WHERE casting.ord = 1
    ) AS a
    JOIN
    (
      SELECT movie.title, casting.movieid
      FROM movie
      JOIN casting ON movie.id = casting.movieid
      JOIN actor ON casting.actorid = actor.id
      WHERE actor.name = 'Julie Andrews'
    ) AS b
    ON a.movieid = b.movieid

14. Obtain a list in alphabetical order of actors who've had at least 30
starring roles.

    SELECT a.name
    FROM
    (
      SELECT actor.name, COUNT(casting.movieid) star_count
      FROM actor
      JOIN casting ON actor.id = casting.actorid
      WHERE casting.ord = 1
      GROUP BY actor.name
    ) AS a
    WHERE star_count >=30
    ORDER BY a.name

List the 1978 films by order of cast list size.

    SELECT movie.title, COUNT(*) cast_size
    FROM movie
    JOIN casting ON movie.id = casting.movieid
    WHERE movie.yr = 1978
    GROUP BY movie.title
    ORDER BY cast_size DESC

List all the people who have worked with 'Art Garfunkel'.

    SELECT DISTINCT actor.name
    FROM actor
    JOIN casting ON actor.id = casting.actorid
    JOIN
    (
      SELECT casting.movieid
      FROM actor
      JOIN casting ON actor.id = casting.actorid
      WHERE actor.name = 'Art Garfunkel'
    ) AS a
    ON casting.movieid = a.movieid
    WHERE actor.name <> 'Art Garfunkel'


USING NULLS
-----------

List the teachers who have NULL for their department.

    SELECT name
    FROM teacher
    WHERE dept IS NULL

Note the INNER JOIN misses the teacher with no department and the
department with no teacher.

    SELECT teacher.name, dept.name
    FROM teacher
    INNER JOIN dept
    ON (teacher.dept = dept.id)

Use a different JOIN so that all teachers are listed.

    SELECT teacher.name, dept.name
    FROM teacher
    LEFT JOIN dept
    ON (teacher.dept = dept.id)

Use a different JOIN so that all departments are listed.

    SELECT teacher.name, dept.name
    FROM teacher
    RIGHT JOIN dept
    ON (teacher.dept = dept.id)

Show teacher name and mobile number or '07986 444 2266'

    SELECT name, COALESCE(mobile, '07986 444 2266') mobile
    FROM teacher

Use the COALESCE function and a LEFT JOIN to print the name and
department name. Use the string 'None' where there is no department.

    SELECT teacher.name, COALESCE(dept.name, 'None')
    FROM teacher
    LEFT JOIN dept
    ON teacher.dept = dept.id

Use COUNT to show the number of teachers and the number of mobile phones.

    SELECT COUNT(teacher.id) num_teachers,
      COUNT(mobile) num_mobiles
    FROM teacher

Use COUNT and GROUP BY dept.name to show each department and the number of
staff. Use a RIGHT JOIN to ensure that the Engineering department is listed.

    SELECT dept.name, COUNT(teacher.id) num_teachers
    FROM teacher
    RIGHT JOIN dept
    ON teacher.dept = dept.id
    GROUP BY dept.name

Use CASE to show the name of each teacher followed by 'Sci' if the teacher
is in dept 1 or 2 and 'Art' otherwise.

    SELECT teacher.name,
      CASE WHEN dept IS NULL THEN 'Art' ELSE 'Sci' END AS dept_type
    FROM teacher

Use CASE to show the name of each teacher followed by 'Sci' if the
teacher is in dept 1 or 2 show 'Art' if the dept is 3 and 'None' otherwise.

    SELECT teacher.name,
      CASE
    	WHEN dept IS NULL THEN 'None'
    	WHEN dept = 3 THEN 'Sci'
   		ELSE 'Sci'
      END AS dept_type
    FROM teacher


SELF JOIN
---------

How many stops are in the database.

    SELECT COUNT(stops.id)
    FROM stops

Find the id value for the stop 'Craiglockhart'

    SELECT id
    FROM stops
    WHERE name = 'Craiglockhart'

Give the id and the name for the stops on the '4' 'LRT' service.

    SELECT id, name
    FROM stops
    JOIN route ON stops.id = route.stop
    WHERE num = 4
    AND company = 'LRT'

The query shown gives the number of routes that visit either London Road
(149) or Craiglockhart (53). Run the query and notice the two services that
link these stops have a count of 2. Add a HAVING clause to restrict the
output to these two routes.

    SELECT company, num, COUNT(*)
    FROM route
    WHERE stop = 149 OR stop = 53
    GROUP BY num
    HAVING COUNT(*) = 2

Execute the self join shown and observe that b.stop gives all the places
you can get to from Craiglockhart, without changing routes. Change the query
so that it shows the services from Craiglockhart to London Road.

    SELECT a.company, a.num, a.stop, b.stop
    FROM route a
    JOIN route b ON (a.num = b.num)
    WHERE a.stop = 53
    AND b.stop = 149

The query shown is similar to the previous one, however by joining two
copies of the stops table we can refer to stops by name rather than by
number. Change the query so that the services between 'Craiglockhart' and
'London Road' are shown. If you are tired of these places try 'Fairmilehead'
against 'Tollcross'

    SELECT a.company, a.num, stopa.name, stopb.name
    FROM route a
    JOIN route b ON (a.num = b.num)
    JOIN stops stopa ON (a.stop = stopa.id)
    JOIN stops stopb ON (b.stop = stopb.id)
    WHERE stopa.name = 'Craiglockhart'
    AND stopb.name = 'London Road'

Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith')

    SELECT DISTINCT a.company, a.num
    FROM route a
    JOIN route b ON a.num = b.num
    WHERE a.stop = 115
    AND b.stop = 137

Give a list of the services which connect the stops 'Craiglockhart' and
'Tollcross'

    SELECT a.company, a.num
    FROM route a
    JOIN route b ON (a.num = b.num)
    JOIN stops stopa ON (a.stop = stopa.id)
    JOIN stops stopb ON (b.stop = stopb.id)
    WHERE stopa.name = 'Craiglockhart'
    AND stopb.name = 'Tollcross'

Give a distinct list of the stops which may be reached from 'Craiglockhart' by taking one bus. Include the company and bus no. of the relevant services.

    SELECT DISTINCT stopb.name, b.company, b.num
    FROM route a
    JOIN route b ON (a.num = b.num AND a.company = b.company)
    JOIN stops stopa ON (a.stop = stopa.id)
    JOIN stops stopb ON (b.stop = stopb.id)
    WHERE stopa.name = 'Craiglockhart'

Show that it's possible to get from Craiglockhart to Sighthill using
2 buses. (this DB doesn't make this easy!)

    SET SQL_BIG_SELECTS=1;
    SELECT DISTINCT stopa.name, stopd.name
    FROM stops stopa
    JOIN route a ON (stopa.id = a.stop)
    JOIN route b ON (a.num = b.num AND a.company = b.company)
    JOIN route c ON (b.stop = c.stop AND b.company = c.company)
    JOIN route d ON (c.num = d.num AND c.company = d.company)
    JOIN stops stopd ON (stopd.id = d.stop)
    WHERE stopa.name = 'Craiglockhart'
    AND stopd.name = 'Sighthill'

The above didn't work through no fault of my own, but the code below did:

    SELECT stopa.name, stopb.name
    FROM route a
    JOIN route b ON (a.num = b.num)
    JOIN stops stopa ON (a.stop = stopa.id)
    JOIN stops stopb ON (b.stop = stopb.id)
    WHERE stopa.name = 'Craiglockhart'
    AND stopb.name = 'Sighthill'






