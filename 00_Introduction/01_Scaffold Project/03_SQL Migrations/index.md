### Maitaining an RDBMS for Your Web Service

Since this is meant to be a light course, for now please copy the `migrations/` and `seed/` directories from [here](https://github.com/exlskills/demo-go-webservice/tree/master/migrations) and [here](https://github.com/exlskills/demo-go-webservice/tree/master/seed), respectively. The general idea is that  we'll create `.sql` files that can be used manually (or with a mgiration tool) to create migrations and seeds that we can easily manage.

For more in-depth docs and reference, please check out [migrate](https://github.com/golang-migrate/migrate) which is the tool that our migrations will be compatible with!

The following sections will assume that you have copied (and hopefully reviewed!) the `migrations/` and `seed/` directories from the GitHub repo, but have not yet executed them anywhere.
