# SimpleMysql
An ultra simple wrapper for `mysql-connector-python` with very basic functionality.

##### Authors
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
    <tr>
        <td align="center">
            <a href="https://github.com/lilkingjr1">
                <img src="https://avatars.githubusercontent.com/u/4533989" width="50px;" alt=""/><br /><sub><b>David Wolfe</b></sub>
            </a>
            <br />
            <a href="https://github.com/lilkingjr1/simplemysql/commits?author=lilkingjr1" title="Codes">💻</a>
            <a href="https://github.com/lilkingjr1/simplemysql/commits?author=lilkingjr1" title="Maintains">🔨</a>
        </td>
        <td align="center">
            <a href="https://github.com/milosb793">
                <img src="https://avatars.githubusercontent.com/u/5012355" width="50px;" alt=""/><br /><sub><b>Milosh Bolic</b></sub>
            </a>
            <br />
            <a href="https://github.com/knadh/simplemysql/commits?author=milosb793" title="Codes">💻</a>
            <a href="https://github.com/knadh/simplemysql/commits?author=milosb793" title="Contributor">💡</a>
        </td>
        <td align="center">
            <a href="https://github.com/knadh">
                <img src="https://avatars.githubusercontent.com/u/547147" width="50px;" alt=""/><br /><sub><b>Kailash Nadh</b></sub>
            </a>
            <br />
            <a href="https://github.com/knadh/simplemysql/commits?author=knadh" title="Codes">💻</a>
            <a href="https://github.com/knadh/simplemysql/commits?author=knadh" title="Original Creator">⭐</a>
            <a href="https://github.com/knadh/simplemysql/commits?author=knadh" title="Retired from Development">💤</a>
        </td>
    </tr>
</table>
<!-- markdownlint-enable -->
<!-- prettier-ignore-end -->
Licensed under GNU GPL v2

## Installation
With pip3

```pip3 install git+https://github.com/lilkingjr1/simplemysql```

Or from the source

```python setup.py install```

# Usage
## For normal connection
```python
from simplemysql import SimpleMysql

db = SimpleMysql(
	host="127.0.0.1",
	port="3306",
	db="mydatabase",
	user="username",
	passwd="password",
	keep_alive=True # try and reconnect timedout mysql connections?
)
```
## For SSL Connection
```python
from simplemysql import SimpleMysql

db = SimpleMysql(
    host="127.0.0.1",
	port="3306",
    db="mydatabase",
    user="username",
    passwd="password",
    ssl = {'cert': 'client-cert.pem', 'key': 'client-key.pem'},
    keep_alive=True # try and reconnect timedout mysql connections?
)
```

```python
# insert a record to the <em>books</em> table
db.insert("books", {"type": "paperback", "name": "Time Machine", "price": 5.55, year: "1997"})

book = db.getOne("books", ["name"], ["year = 1997"])

print "The book's name is " + book['name']
```

# Query methods
insert(), update(), insertBatch(), insertOrUpdate(), delete(), getOne(), getAll(), lastId(), query()

## insert(table, record{})
Inserts a single record into a table.

```python
db.insert("food", {"type": "fruit", "name": "Apple", "color": "red"})
db.insert("books", {"type": "paperback", "name": "Time Machine", "price": 5.55})
```

## update(table, row{}, condition[])
Update one more or rows based on a condition (or no condition).

```python
# update all rows
db.update("books", {"discount": 0})

# update rows based on a simple hardcoded condition
db.update("books",
	{"discount": 10},
	["id=1"]
)

# update rows based on a parametrized condition
db.update("books",
	{"discount": 10},
	("id=%s AND year=%s", [id, year])
)
```
## insertBatch(table, rows{})
Insert Multiple values into table.

```python
# insert multiple values in table
db.insertBatch("books", [{"discount": 0},{"discount":1},{"discount":3}])
```

## insertOrUpdate(table, row{}, key)
Insert a new row, or update if there is a primary key conflict.

```python
# insert a book with id 123. if it already exists, update values
db.insertOrUpdate("books",
		{"id": 123, type": "paperback", "name": "Time Machine", "price": 5.55},
		"id"
)
```

## getOne(table, fields[], where[], order[], limit[])
## getAll(table, fields[], where[], order[], limit[])
Get a single record or multiple records from a table given a condition (or no condition). The resultant rows are returned as namedtuples. getOne() returns a single namedtuple, and getAll() returns a list of namedtuples. `None` is returned if no record(s) can be found.

```python
book = db.getOne("books", ["id", "name"])
```

```python
# get a row based on a simple hardcoded condition
book = db.getOne("books", ["name", "year"], ("id=1"))
```

```python
# get multiple rows based on a parametrized condition
books = db.getAll("books",
	["id", "name"],
	("year > %s and price < %s", [year, 12.99])
)
```

```python
# get multiple rows based on a parametrized condition with an order and limit specified
books = db.getAll("books",
	["id", "name", "year"],
	("year > %s and price < %s", [year, 12.99]),
	["year", "DESC"],	# ORDER BY year DESC for descending (ASC for ascending)
	[0, 10]			# LIMIT 0, 10
)
```
## lastId()
Get the last insert id
```python
# get the last insert ID
db.lastId()
```

## lastQuery()
Get the last query executed
```python
# get the SQL of the last executed query
db.lastQuery()
```

## delete(table, fields[], condition[], order[], limit[])
Delete one or more records based on a condition (or no condition)

```python
# delete all rows
db.delete("books")

# delete rows based on a condition
db.delete("books", ("price > %s AND year < %s", [25, 1999]))
```

## query(table)
Run a raw SQL query. The MySQLdb cursor is returned.

```python
# run a raw SQL query
db.query("DELETE FROM books WHERE year > 2005")
```

## commit()
Insert, update, and delete operations on transactional databases such as innoDB need to be committed

```python
# Commit all pending transaction queries
db.commit()
```
