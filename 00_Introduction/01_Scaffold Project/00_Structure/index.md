### Go Web Service Project Structure

This is the essential structure for a web service that would rely on a relational datastore:

```bash
config/
    config.go # Manage our service-wide configuration

controllers/
    v1 # Good practice to version them!
        gophers.go # This is the route that we will be implementing in this course!
        v1.go # Good practice to have a 'central' file for the package to setup package-wide vars, init, etc.
    # We could add other version directories as we need them

middleware/
    middleware.go # We're going to put our custom middleware here!

models/
    gopher.go # This is the example data model that we're going to be implementing in this course!
    models.go # This file will setup package-wide vars and run any+all required init for DBs, etc.

migrations/ # This is where would put our SQL DB migrations
    1_init.up.sql # Init up
    1_init.down.sql # Init down (revert init up)

seed/ # SQL Seed files (different from migrations in that it's not used for production -- only dev!)
    1_seed.up.sql # Seed up db
    1_seed.down.sql # Delete seeded data

reqctx/
    reqctx.go # Manage our request context data (not stateful sessions!)

routes/
    routes.go # Spec out our router configuration

Dockerfile # Production Dockerfile for deployment
main.go # Entrypoint ('main function')
```

### Scaffolding our Project

We will create these files and directories as we go, however, you can also create the structure ahead of time

### Variations

Depending on your project, you might have some common variations on this structure, such as:

* To setup unit tests, you would have *_test.go` variations of the files for which you have unit testing

* No-SQL where you would remove the `migrations` directory

* Serving HTML templates, where you would have a `views` directory (similarly structured to controllers) and then likely a `templates` directory for managing your actual HTML templates
