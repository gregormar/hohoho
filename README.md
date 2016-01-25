###Zadanie MongoDB

#####Import pliku bazy danych Reddit RC_2015-01 do bazy MongoDB wersja 3.0.7

* pierwszy przykładowy json z kolekcji (wraz ze wszystkimi rekordami):
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

WSTAW OBRAZEK

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

![json](img/obraz3.png)

czyli wszystko ok, ilość się zgadza


* wyświetlenie 10 subredditów na literę "s"
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
* zliczenie wszystkich wpisów w subreddicie "marvelstudios":
```sh
db.reddit.find({subreddit: "marvelstudios"}).count()
27924
```
* wyświetlenie grupowania 5 najbardziej aktywnych subredditów:
```sh
db.reddit.aggregate([ 
{$group:{_id: "$subreddit", count:{$sum: 1}}},
{$sort:{count: -1}},
{$limit: 5}
]);
{
  "result": [
    {
      "_id": "AskReddit",
      "count": 4712795
    },
    {
      "_id": "nfl",
      "count": 932460
    },
    {
      "_id": "funny",
      "count": 930098
    },
    {
      "_id": "leagueoflegends",
      "count": 904297
    },
    {
      "_id": "pics",
      "count": 778942
    }
  ],
  "ok": 1
}
```
![chart](img/chart1.png)

* znajdź ostatni wpis w kolekcji:
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
