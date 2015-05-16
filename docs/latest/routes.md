---
title: Routes
---

You define route handlers using the four verb methods (`get`, `post`, `put` and `del`). You can use shorthands, return a plain object, or write custom function handlers.

The status code defaults to the following, based on the verb being used for the route:

  - GET is 200
  - PUT is 204
  - POST is 201
  - DEL is 204

## Shorthands

```js
this.verb(path, shorthand[, responseCode]);
```

*shorthand* can either be a string, an array, or `undefined`, depending on which shorthand you're using. View [the reference](../shorthands) for all available shorthands.

Examples:

```js
this.get('/api/users');
this.put('/api/users/:id');
this.get('/posts/:id', ['post', 'comments']);
```

## Object handler

```js
this.verb(path, object[, responseCode]);
```

*object* is a POJO that's returned for this route.

Example:

```js
this.get('/api/users/current', {id: 1, name: 'Link'});
this.get('/some/secret', {message: 'unauthorized'}, 404);
```

## Function handler

Write a custom function to handle this route.

```js
this.verb(path, function(db, request) {
  // your code
}[, responseCode]);
```

The function handler you define takes two parameters, **db** (your Mirage server's database) and **request** (the Pretender request object). Consult [the database's API](../database) for how to interact with the db and [Pretender's docs](https://github.com/trek/pretender) for more info on the request object.

Return the data you want as plain JS - it will be stringified and sent as the response body of your request. You can also return an instance of `Mirage.Response` to dynamically set headers and the status code.

Examples:

```js
this.del('/api/users/:id', function(db, request) {
  var id = request.params.id;
  db.users.delete(id);
  // Delete related addresses
  db.addresses.delete({user_id: id});
  
  return {};
});

this.post('/api/messages', function(db, request) {
  var params = JSON.parse(request.requestBody);

  if (!params.title) {
    return new Mirage.Response(400, {a: 'header'}, {message: 'title cannot be blank'});
  } else {
    return db.messages.insert(params);
  }
});
```