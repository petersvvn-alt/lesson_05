# <center>TMS</center>

## <center>Home Work #5</center>

## Задание 1 - Установить Mongo DB
1. Для установки пользовался командами:
 ``` bash
 sudo apt-get install gnupg curl
 curl -fsSL https://pgp.mongodb.com/server-7.0.asc |sudo gpg  --dearmor -o /etc/apt/trusted.gpg.d/mongodb-server-7.0.gpg
 echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
 sudo apt-get update
 sudo apt-get install -y mongodb-org
 ```
2. Cоздать таблицу data создать пользователя manager, у которого будет доступ
только на чтение этой таблицы.

- Создание таблицы `data` и пользователя `manager`
``` javascript
use mydb
db.data.insertOne({ name: "test", value: 1 })
db.createRole({
  role: "readData",
  privileges: [ { resource: { db: "mydb", collection: "data" }, actions: ["find"] } ],
  roles: []
})
db.createUser({
  user: "manager",
  pwd: "test321",
  roles: [ { role: "readData", db: "mydb" } ]
})
```

- Проверка прав пользователя `manager`

Подключаемся к MongoDB и проверяем:

``` javascript
> show collections
data
> db.data.insertOne({ name: "test2", value: 2 })
{
	"acknowledged" : true,
	"insertedId" : ObjectId("6a3838d7da8370e11a64311f")
}
> db.getUser("manager")
{
	"_id" : "mydb.manager",
	"userId" : UUID("2d126f5d-54c1-431f-b661-f3d74d4d39f2"),
	"user" : "manager",
	"db" : "mydb",
	"roles" : [
		{
			"role" : "readData",
			"db" : "mydb"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
}
```