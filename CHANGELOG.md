## 1.0.15 (2020-03-08)
#### Bug fixing
Fixed bug with cache entity links.

For example you create some Task entity that have the next json representation:
```
Task JSON example:
{
  "id": 1,
  ...
  "autoReduce": false,
  "_links": {
    "self": {
      "href": "http://localhost:8080/api/v1/tasks/1"
    },
    "category": {
      "href": "http://localhost:8080/api/v1/tasks/1/category"
    }
    ...
  }
}
```

Suppose that now you want see the task category entity and you invoke the `getRelation` method with 'category' relation name on the task entity.

When you get the category this data will be cached to prevent unnecessary request to the server when you will again get Task category.

```
TaskCategory JSON example:

{
  "id" : 1,
  "name" : "Default",
  ...
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/api/v1/task-categories/1"
    },
    ...
  }
}
```

**Important!** Link to the category from the task it's not canonical link to the concrete category it's link allows to get current category to the task.

When you will update the task category you will not change this link, but a data returned from this link request will be changed.

```
TaskCategory JSON example:

{
  "id" : 2,
  "name" : "Work",
  ...
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/api/v1/task-categories/2"
    },
    ...
  }
}
```

And if you will try get a new category using the `getRelation` method you will get the old category because you will get it from the cache (we don't change the link to the category from the task when the category was changed).

After fixing this bug all links data will be evict after any change Task entity and now you will have request to the server when change task category to get fresh the category data. 

## 1.0.14 (2020-03-06)
#### Bug fixing
Fixed bug with protected constructor in Resource class provided by [issue](https://github.com/lagoshny/ngx-hal-client/issues/5)

## 1.0.13 (2020-03-05)
#### Bug fixing
Fixed bug by [issue](https://github.com/lagoshny/ngx-hal-client/issues/4)

## 1.0.12 (2020-03-03)
Updated Angular version to 9 with disabled Ivy.

## 1.0.9 (2019-11-15)
#### Features
- Added to `Resource` class `getSelfLinkHref` method to get self href link that can be used as param for server endpoint.
- Now you can use `Reosurce` type as `HalParam`. In this case your `Resource` class need to have self href link, because when will do converting `HalParams` in request params, for `Resource` param type will use the self href url link where resource can be found.

## 1.0.8 (2019-10-13)
Updated Angular version.

## 1.0.7 (2019-09-16)

#### Features
Added to `ResourcePage` additional properties:

- totalPages - count pages
- totalElements - count elements on the page

## 1.0.6 (2019-08-19)

#### Features
Added `searchPage` method to `RestService` that allows getting a list of resources wrapped in page object thereby allowing to receive resources in parts for the more comfortable display of data to the user. 
`searchPage` method returns `ResourcePage` model which has `resources` property where holds array returned resources and also has next methods:

- hasFirst - return `true` when returned page has link to first page, otherwise `false`
- hasLast - return `true` when returned page has link to last page, otherwise `false`
- hasNext - return `true` when returned page has link to next page, otherwise `false`
- hasPrev - return `true` when returned page has link to prev page, otherwise `false`
- first - perform request by first link to get first page
- last - perform request by last link to get last page
- next - perform request by next link to get next page
- prev - perform request by prev link to get prev page
- page - perform request to the specified page
- size - perform request with specified size
- sortElements - perform request with specified sort params

## 1.0.5 (2019-08-11)

#### Bugs
##### PostRelation/PathRelation methods
Was: `postRelation/pathRelation` returned the same `Resource` object instance on which the method was called.

Now:  `postRelation/pathRelation` returned new `Resource` object instance.

## 1.0.4 (2019-08-11)

#### Features
For `postRelation/pathRelation` methods added converting result to `Resource` class.

## 1.0.3 (2019-08-11)

#### Features

Added ability to pass http request params to `postRelation/pathRelation` methods.

To do that you need to pass `LinkOptions` object as last parameter to `postRelation/pathRelation` methods.

`LinkOptions` consists of

````
{
   strictParams: true | false,
   params: {
     name: valueAsString
   }
}  
```` 

If `strictParams` is false (default value) then **all** passed `params` will be added in relation's link url.

When `strictParams`is true then only params contained in relation's href (e.g. `{?projection}`) and mapped to passed params will be added in relation's link url.
 
For example, you have `Task` resource with custom relation `updateStatus` and `link` like this `http://host.com/api/v1/tasks/task_id/update/status{?projection}`. 
After update tasks status you want to get updated task with projection. 

If you will post relation with `strictParams` then only `projection` param will be added, other params will be ignored. 
On the other hand without `strictParams` all passed params will be added to link's href.

## 1.0.2 (2019-08-09)

#### Features

Added ability to perform `post/path` requests for resource's relations.

For example, your resource has some relation that mean action and you need to perform post/patch request and pass some parameters.

Now, you can use new `postRelation/pathRelation` methods to do it.
