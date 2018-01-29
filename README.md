# ember-query-layer

An experiment in more declarative data loading.

This is experimental! It doesn't really work yet, and won't have a stable
API for a while.

To simplify and get something working, it only supports a very limited
subset of GraphQL queries: a single query for a single root field, with
a single optional `id` argument. The root field must match the name of
an existing Ember Data model. Include the id to fetch a single record,
or omit it to fetch all. No nested fields, mutations, fragments, or
directives are supported.

This will be expanding as we figure out how to properly 

## Installation

* `ember install ember-query-layer`

## Planned Usage
WAIT! Using `model()` isn't really ideal, since it doesn't always get
called. Was there a new hook called `data()` or something? Or could we
reopen link-to somehow and check whether the target route has a
query-layer model hook?

This doesn't work yet! But practicing some README-driven development,
here's what I'm thinking:

```js
import { query } from 'ember-query-layer';
export default Route.extend({
  model: query`
    query PostsRoute {
      post {
        comments {
          author
        }
      }
    }
  `
});
```

This will have a similar effect to (although without the confusing
semantics of reload, backgroundReload, or async relationships. A single
promise is returned, which resolves with all the data you requested.
Note - you don't need to specify scalar fields (only relationships), because they're already
specified in your DS.Model, and REST endpoints usually return the whole
record anyway)
```js
export default Route.extend({
  model() {
    return this.store.findAll('post', {
      include: 'comments,comments.author'
    });
  }
});
```

You can use 

Equivalent to `store.findRecord()`:
```js
import { Route } from '@ember/routing';
import { query } from 'ember-query-layer';

export default Route.extend({
  model: query`
    query PostRoute($post_id: ID) {
      post(id: $post_id)
    }
  `
});
```
