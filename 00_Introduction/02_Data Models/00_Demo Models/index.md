### Representing Data in Go

The intermediary state between our database/datastore and output JSON (or HTML views, etc.) is going to be our data model `struct`s in the `models/` directory. We will also use this directory for specifying our request/response (frequently JSON) models, as well as the various DB-related models that we might need, i.e., model for the result of a `JOIN` query.

### Gophers!

Since we're storing "gophers" in our MySQL database, let's create a model in `models/gopher.go` to represent that DB table:

```go
package models

import (
	"time"
)

type Gopher struct {
	ID        uint64    `json:"id"`
	FullName  string    `json:"fullName"`
	Headline  string    `json:"headline"`
	AvatarURL string    `json:"avatarUrl"`
	CreatedAt time.Time `json:"-"`
	UpdatedAt time.Time `json:"-"`
}

const getGophersPaginatedQuery = `SELECT * FROM gopher ORDER BY created_at DESC LIMIT ? OFFSET ?;`

func (gopher *Gopher) FindByID() error {
	return db.Where("id = ?", gopher.ID).First(&gopher).Error
}

func (gopher *Gopher) Create() error {
	return db.Create(&gopher).Error
}

func (gopher *Gopher) UpdateMeta() error {
	return db.Table("gopher").Where("id = ?", gopher.ID).Updates(map[string]interface{}{
		"full_name":  gopher.FullName,
		"headline":   gopher.Headline,
		"avatar_url": gopher.AvatarURL,
	}).Error
}

func GetGophers(limit, offset int64) ([]Gopher, error) {
	var gophers []Gopher
	// Technically this query could go through GORM natively, but just showing off raw SQL query functionality!
	if err := db.Raw(getGophersPaginatedQuery, limit, offset).Scan(&gophers).Error; err != nil {
		return nil, err
	}
	return gophers, nil
}

```

### User (virtual, for convenience)

Since frequently applications will have users, we'll add the boilerplate model in `models/user.go` since it is used in some of our middleware code, however, the DB table does not exist, so we will mock our DB methods.

```go
package models

type User struct {
    ID       uint64 `json:"-"`
    FullName string `json:"-"`
}

func (user *User) FindByID() error {
    user.ID = 1
    user.FullName = "Demo User"
	return nil
}

```
