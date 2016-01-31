###MongoDB

#####Import pliku bazy danych Reddit 

[logo2]: http://screenshu.com/static/uploads/temporary/k0/kf/ko/w83ihb.jpg "czas"
![alt text][logo2]

* 1.pierwszy przykładowy json z kolekcji :
```sh
db.reddit.findOne()
{
	"_id" : ObjectId("56a50ed6ee29eb04e12f0b60"),
	"distinguished" : null,
	"id" : "cnas8zw",
	"archived" : false,
	"author" : "RedCoatsForever",
	"score" : 3,
	"created_utc" : "1420070400",
	"downs" : 0,
	"body" : "But Mill's career was way better. Bentham is like, the Joseph Smith to Mill's Brigham Young.",
	"link_id" : "t3_2qv6c6",
	"name" : "t1_cnas8zw",
	"score_hidden" : false,
	"controversiality" : 0,
	"subreddit_id" : "t5_2s4gt",
	"edited" : false,
	"retrieved_on" : 1425124282,
	"ups" : 3,
	"author_flair_css_class" : "on",
	"gilded" : 0,
	"author_flair_text" : "Ontario",
	"subreddit" : "CanadaPolitics",
	"parent_id" : "t1_cnas2b6"
}

```
Historia Procesora

[logo]: http://s1.fotowrzut.pl/MSWICMLLI9/1.jpg "procek"
![alt text][logo]

Procesory były obciążone równomiernie od 10 do 86 procent. W trakcie importu oba rdzenie działały wlaściwie identycznie, w tych samych momentach nasilając swoją aktywność i redukując ja prawie do 0.

Połączyłem się z mongo, przeszłem do bazy testy i wybrałem kolekcję reddit:
```sh
mongo
MongoDB shell version: 3.0.7
connecting to: test
> show dbs
local	0.203125GB
test	37.935546875GB
> use test
switched to db test
> show collections
reddit
system.indexes
```
zliczenie jsonow:

[logo1]: http://screenshu.com/static/uploads/temporary/55/6t/a1/39zu9y.jpg
 "json"
![alt text][logo1]

czyli wszystko ok, ilość się zgadza


* 2.wyświetlenie 10 subredditów na literę "s"
```sh
db.reddit.find({subreddit: /^s/}, {_id:0, subreddit:1}).limit(10)

{ "subreddit" : "summonerschool" }
{ "subreddit" : "sausagetalk" }
{ "subreddit" : "swtor" }
{ "subreddit" : "steroids" }
{ "subreddit" : "smashbros" }
{ "subreddit" : "soccer" }
{ "subreddit" : "sports" }
{ "subreddit" : "serialpodcast" }
{ "subreddit" : "soccer" }
{ "subreddit" : "smashbros" }

```
* 3.zliczenie wszystkich wpisów w subreddicie "soccer":
```sh
db.reddit.find({subreddit: "soccer"}).count()
172547

```
* 4.wyświetlenie grupowania 5 najmniej aktywnych subredditów:
```sh

db.reddit.aggregate([ 
... {$group:{_id: "$subreddit", count:{$sum: 1}}},
... {$sort:{count: 1}},
... {$limit: 5}
... ]);

{ "_id" : "lesbianromance", "count" : 1 }
{ "_id" : "Arcestir", "count" : 1 }
{ "_id" : "FirstLook", "count" : 1 }
{ "_id" : "suomirap", "count" : 1 }
{ "_id" : "HutCoinSelling", "count" : 1 }

```


* 5.znajdź ostatni wpis w kolekcji:
```sh
db.reddit.findOne( {$query:{}, $orderby:{$natural:-1}} )
{
	"_id" : ObjectId("56579d9ec40dd605eb210312"),
	"author_flair_text" : "RRRAURGH!",
	"gilded" : 0,
	"score_hidden" : false,
	"id" : "co77gzt",
	"parent_id" : "t1_co6zqmw",
	"distinguished" : null,
	"ups" : 3,
	"downs" : 0,
	"created_utc" : "1422748799",
	"name" : "t1_co77gzt",
	"body" : "You can already shoot through walls without Shred.",
	"author_flair_css_class" : "valkyr-bastet",
	"subreddit_id" : "t5_2urg0",
	"link_id" : "t3_2uazsm",
	"controversiality" : 0,
	"edited" : false,
	"retrieved_on" : 1424281770,
	"author" : "blolfighter",
	"subreddit" : "Warframe",
	"archived" : false,
	"score" : 3
}


```
6.Wyświetlenie 3 wpisów autora "blazefalcon" (pominięcie pierwszych 10)

```sh

db.reddit.find({author: "blazefalcon"}, {_id:0, body:1}).skip(10).limit(3)

{ "body" : "I'm a huge GT-R fanboy, but I will admit is is not the prettiest car. That being said, it's not ugly, it's an incredible car, and who cares what other people think if you like it. My friends all tell me that my Firebird looks like a catfish, but I love it and that's all I care about. They're not footing the bill, their opinion on looks shouldn't matter. " }

{ "body" : "I'd look into James Edition, they get a good view count and it's like Craigslist for the wealthy. " }

{ "body" : "At 95 it's a stretch but i have to say Jaguar XJ220. First car I ever fell  in love with. " }

```
 7.Wywietlenie postów z wynikiem wikszym od 3000. ( troche tego bylo, po wpisaniu "it" pojawialo sie wiecej, wiec wymienilem tylko kilka przykladowych)
 
 ```sh
 
db.reddit.find({score: { $gte: 3000}}, {_id:0, author:1, body:1, subreddit:1, score:1});

{ 
"subreddit" : "explainlikeimfive", 
"body" : "Cocaine also gets into your bloodstream quickly through your mouth and if it is of high quality you can feel it's effects rather quickly.\n\nSource: uh....", 
"author" : "gwin69", 
"score" : 3192 }

{ "subreddit" : "videos", 
"body" : "They must take a lot of fucking selfies for this not to be obvious every time she tries.", 
"author" : "JakJakAttacks", 
"score" : 3967 }

{ "author" : "nothingpersonal_", 
"score" : 4460, 
"body" : "i'm fairly drunk atm but that doesn't look very hidden to me.", 
"subreddit" : "WTF" }

{ "author" : "isolatedpawn22", 
"subreddit" : "tifu", 
"body" : "Dad probably laughed his ass off later.", 
"score" : 3066 }

{ "author" : "DoubleVixen", 
"subreddit" : "AskReddit", 
"body" : "We have a gas station and a grocery store. No there is absolutely no reason to stay here. Please take me with you.", 
"score" : 3139 }
 
"author" : "boernelakebadboy3" }
{ "body" : "\"I live in the upper class part of town. I'd stay away from the southern part of.. Oh what am I saying, you probably live there.\"\n\nJust enough to make you hate me and head down there to spite me.", "author" : "djilluminate", "score" : 3069, 
"subreddit" : "AskReddit" }

```
8. zliczenie wpisow autora Drumlin
```sh

db.reddit.find({author: "Drumlin"}).count()

```

### Postgres

#####Import pliku bazy danych Reddit 

Do zaimportowania rozpakowanego pliku RC_2015-01 użyłam programu pgfutter pobranego z  [github.com/lukasmartinelli](https://github.com/lukasmartinelli/pgfutter).

Historia Procesora:

[logo3]: http://screenshu.com/static/uploads/temporary/qh/4o/a9/w63q3b.jpg "czas"
![alt text][logo3]

Troche mniejsze obciazenie procesora w stosunku do importowania bazy danych do mongoDB

Zliczylem jsony
```sh
postgres=# select count(*) from import.rc_2015_01;
 count
 ----------
 53851542

```

1. znajdź pierwszy:
```sh
postgres=# select * from import.rc_2015_01 LIMIT 1;
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 {"score_hidden":false,"name":"t1_cnas8zv","link_id":"t3_2qyr1a","body":"Most of us have some family members like this. *Most* of my family is like this. ","downs":0,"created_utc":"1420070400","score":14,"author":"YoungModern","distinguished":null,"id":"cnas8zv","archived":false,"parent_id":"t3_2qyr1a","subreddit":"exmormon","author_flair_css_class":null,"author_flair_text":null,"gilded":0,"retrieved_on":1425124282,"ups":14,"controversiality":0,"subreddit_id":"t5_2r0gj","edited":false}+
 
(1 wiersz)
```
2. wyświetlenie 10 subredditów na literę "s".
```sh
SELECT data->>'subreddit' AS subreddit FROM import.rc_2015_01 WHERE data->>'subreddit' like ('s%') LIMIT 5;    
----------------
summonerschool
sausagetalk
swtor
steroids
smashbros
soccer
sports
serialpodcast
soccer
smashbros
```

#####  MongoDB a PostgreSQL:

|Polecenie					| MongoDB 		| PostgreSQL 				|
|-----------------------------------------------|-----------------------|---------------------------------------|
|wersja 					|3.0.7			|9.4.5					|
|import bazy danych	         			|prosty, w jednej lini rozpakowywanie i import		        |Najpierw musimy rozpakować plik, następnie za pomocą programu pgfutter musimy zaimportować plik do postgresa				|
|Czas zliczenia rekordów			|<1s			|30m					|
|Obsługa poleceń                                |momentami mozna sie pogubic z tymi nawiasami,klamrami itd. | niby bardziej instynktowna ale to raczej wynika z przyzwyczajen
|Obciążenie procesora w trakcie importu		|mongoimport: większe (15-95%)	|pgfutter: mniejsze (10-60%)	|
|Łatwość wyszukiwania jsonów			|czytelne osobne rekordy	|wszystko w jednej lini co utrudnia odczytywanie	|

Ogólnie MongoDB wydaje sie sympatyczniejsze glownie ze wzgledu na czytelnosc przedstawiania danych

###Zadanie 2 GeoJSON

Pobralem plik bazy danych lokalizacji stacji benzynowych Orlen w Polsce orlen.json. Zaimportowalem plik do bazy MongoDB w nastepujacy sposob:

```sh

time mongoimport -d orlen -c stacje < orlen.json

real	0m3.683s
user	0m0.016s
sys	0m0.024s
```
1. przykładowy pierwszy GeoJSON:

```sh
db.stacje.findOne()
{
	"_id" : ObjectId("56a8d6b9629ae9ff8365be9f"),
	"loc" : {
		"type" : "Point",
		"coordinates" : [
			18.662809,
			54.403565
		]
	},
	"name" : "Stacje paliw Orlen",
	"city" : "Gdańsk"
}
```

2. Wyswietlenie przykladowego jsona.

```sh
{ "_id" : ObjectId("56a8d6b9629ae9ff8365bea4"),
"loc" : {
"type" : "Point",
"coordinates" : [
18.777407, 54.074322 ]
},
"name" : "Stacje paliw Orlen", 
"city" : "Tczew" }
```
3. stacje w odleglosci 20 km od Gdanska:

```sh
db.stacje.find({loc: {$near: {$geometry: {type: "Point", coordinates: [ 18.662809, 54.403565]}, $maxDistance: 20000}}}).skip(1)

{ "_id" : ObjectId("56a8d6b9629ae9ff8365bea4"), "loc" : { "type" : "Point", "coordinates" : [ 18.777407, 54.074322 ] }, "name" : "Stacje paliw Orlen", "city" : "Tczew" }
> db.stacje.find({loc: {$near: {$geometry: {type: "Point", coordinates: [ 18.662809, 54.403565]}, $maxDistance: 20000}}}).skip(1)
{ "_id" : ObjectId("56a8d6b9629ae9ff8365bea8"), "loc" : { "type" : "Point", "coordinates" : [ 18.6406, 54.35916 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdańsk" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365beab"), "loc" : { "type" : "Point", "coordinates" : [ 18.57977, 54.39451 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdańsk" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365bea7"), "loc" : { "type" : "Point", "coordinates" : [ 18.68251, 54.34847 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdańsk" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365bea9"), "loc" : { "type" : "Point", "coordinates" : [ 18.63553, 54.3334 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdańsk" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365beaa"), "loc" : { "type" : "Point", "coordinates" : [ 18.6079, 54.32619 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdańsk" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365beb1"), "loc" : { "type" : "Point", "coordinates" : [ 18.477135, 54.380675 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdańsk" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365beb6"), "loc" : { "type" : "Point", "coordinates" : [ 18.5335, 54.51339 ] }, "name" : "Stacje paliw Orlen", "city" : "Gdynia" }
{ "_id" : ObjectId("56a8d6b9629ae9ff8365bea6"), "loc" : { "type" : "Point", "coordinates" : [ 18.791383, 54.291775 ] }, "name" : "Stacje paliw Orlen", "city" : "Koszwały" }
```
http://bl.ocks.org/anonymous/raw/9d6aaa6d26b89dc31353/

4. 5 najblizszych stacji od punktu w Malborku

var pkt= {
 "type" : "Point", 
    "coordinates" : [   19.01104,  54.03809 ] 
};

```sh
db.stacje.find({ loc: {$near: {$geometry: pkt}}},{_id:0,  city:1}).limit(5).toArray()

[
	{
		"city" : "Malbork"
	},
	{
		"city" : "Sztum"
	},
	{
		"city" : "Tczew"
	},
	{
		"city" : "Tczew"
	},
	{
		"city" : "Nowy Dwór Gdański"
	}
]
```
http://bl.ocks.org/anonymous/raw/5a73fb5e1bef5bfb3210/

5. wyswietlenie stacji na lini gdansk Malbork - Chojnice

```sh
db.stacje.find({loc: {$geoIntersects: {$geometry: {type: "LineString", coordinates: [[19.01104,  54.03809] ,[17.579268,  53.707971]]}}}},{_id=0, city:1})
```

6. Polyon. Wyświetlenie stacji(miejscowosci) na danym obszarze

```sh
db.stacje.find({ loc: {$geoWithin : { $geometry: { type : "Polygon",  coordinates: [
...  [ [
...  18.72894287109375,
...  53.71296473440685
...  ],
...  [
...  18.72894287109375,
...  54.10611237685854
...  ],
...  [
...  19.47601318359375,
...  54.10611237685854
...  ],
...  [
...  19.47601318359375,
...  53.71296473440685
...  ],
...  [
...  18.72894287109375,
...  53.71296473440685
...  ]
...  ]
...  ] } } }},{_id:0, city:1} )

{ "city" : "Malbork" }
{ "city" : "Tczew" }
{ "city" : "Tczew" }
{ "city" : "Gniew" }
{ "city" : "Sztum" }
{ "city" : "Kwidzyn" }
{ "city" : "Prabuty" }

```




