### Check out our endpoints!

Now that our server is up and running on port 3333, it's time to check out our two endpoints:

1. `[GET] /v1`: Should always return success and empty response

2. `[GET] /v1/gophers` (Secured by `apiKey=insecure`): Should return our list of gophers, with optional `limit` and `offset` query params for pagination

### Via browser

1. By loading `http://localhost:3333/v1` in your browser, you should see the following:

```json
{"message":"Server healthy","success":true,"data":null}
```

2. By loading `http://localhost:3333/v1/gophers?apiKey=insecure` in your browser, you should see the following:

```json
{"message":"Successfully queried gophers","success":true,"data":{"gophers":[{"id":1,"fullName":"Sasha Varlamov","headline":"CEO @EXLskills","avatarUrl":"https://s3-us-west-2.amazonaws.com/exlskills-instructor-profiles/svarlamov.jpg"},{"id":2,"fullName":"Elliott Saslow","headline":"Python @EXLskills","avatarUrl":"https://s3-us-west-2.amazonaws.com/exlskills-instructor-profiles/swelliott.jpg"},{"id":3,"fullName":"Keenan Olsen","headline":"SQL @EXLskills","avatarUrl":"https://s3-us-west-2.amazonaws.com/exlskills-instructor-profiles/keeno12.jpeg"}]}}
```

### Awesome!

We have now built a working scalable web service that generally adheres to Go, REST, and Docker best practices! 🎉

### To learn more

Please check out our Go x Docker Scalable Web Services guided project that offers an in-depth dive into building idiomatic, secure, and maitainable services with Go and Docker [here](https://exlskills.com/learn-en/projects/building-scalable-web-services-in-golang-with-docker-web_service_golang_docker).
