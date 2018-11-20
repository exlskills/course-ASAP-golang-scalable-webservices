### Setting up DB lib/connection

In this example, we are assuming that we're using MySQL, however, Postgres will be quite similar and docs for that can be found 

### To ORM or not to ORM?

Many Go developers actually just use the built-in `database/sql` package to interact with their database, however, in practice this leaves some things to be desired -- particularly the ability to _map_ record(s) to their `struct` counterpart(s) in your code. This is the primary rationale for using [GORM](http://gorm.io/), as it offers a really easy way to map our data, has some simple query features (like Find, etc.), and also lets us be super expressive with raw SQL queries that we can then map to structs (row-by-row or all at once). 

To learn more about GORM (highly recommended!) check out the docs [here](http://gorm.io/docs/).

### Models Package

In our `models/models.go` file, we will have a `Setup()` function for connecting and setting up the ORM. We'll also expose package-level variables `Log` for (you guessed it) logging, as well as `db` which will be a reference to our ORM that we'll use for querying the DB throughout the `models` package. Careful readers will also question whether or not this is thread-safe, and yes it is -- GORM offers us this guarantee and abstracts things like concurrent connection pooling, etc. Another reason to use a solid ORM/lib.

```go
package models

import (
    "github.com/exlskills/demo-go-webservice/config"
    // Our lightweight ORM
    "github.com/jinzhu/gorm"
    // Use the _ import syntax to ensure that the mysql init()
    // gets run and so that Go doesn't complain about an unused import
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

var Log = config.Cfg().GetLogger()
var db *gorm.DB

func Setup() (err error) {
	db, err = gorm.Open("mysql", config.Cfg().DBPath)
	if err != nil {
		return err
	}

    // Some reasonable pool settings
    db.DB().SetMaxIdleConns(10)
    db.DB().SetMaxOpenConns(100)
    
    // Log all our queries
    // NB: Watch out for this in production. Are you inserting private user data?
    //     That would end up in your logs. Consider your privacy mandate, adjust accordingly.
    db.LogMode(true)

    // Usually simplest to stick to singular table names
    // (less potential for ambiguity and works neatly with struct/model names)
	db.SingularTable(true)

	return nil
}

```

### Update deps

Since we added new dependencies in this file, remember to run `dep ensure -v` again to update our deps.
