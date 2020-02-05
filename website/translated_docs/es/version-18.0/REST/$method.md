---
id: version-18.0-method
title: '$method'
original_id: method
---

This parameter allows you to define the operation to execute with the returned entity or entity selection.

## Available syntaxes

| Syntax                                          | Example                                                                                         | Description                                                                                                |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [**$method=delete**](#methoddelete)             | `POST /Employee?$filter="ID=11"& $method=delete`                                            | Deletes the current entity, entity collection, or entity selection                                         |
| [**$method=entityset**](#methodentityset)       | `GET /People/?$filter="ID>320"& $method=entityset& $timeout=600`                     | Creates an entity set in 4D Server's cache based on the collection of entities defined in the REST request |
| [**$method=release**](#methodrelease)           | `GET /Employee/$entityset/4CANUMBER?$method=release`                                            | Releases an existing entity set stored in 4D Server's cache                                                |
| [**$method=subentityset**](#methodsubentityset) | `GET /Company(1)/staff?$expand=staff& $method=subentityset&   $subOrderby=lastName ASC` | Creates an entity set based on the collection of related entities defined in the REST request              |
| [**$method=update**](#methodupdate)             | `POST /Person/?$method=update`                                                                  | Updates and/or creates one or more entities                                                                |
| [**$method=validate**](#methodvalidate)         | `POST /Employee/?$method=validate`                                                              | Validates the request when adding and/or modifying entities                                                |





## $method=delete

Deletes the current entity, entity collection, or entity selection (created through REST)


### Description

With `$method=delete`, you can delete an entity or an entire entity collection. You can define the collection of entities by using, for example, [`$filter`]($filter.md) or specifying one directly using [`{dataClass}({key})`](%7BdataClass%7D.html#dataclasskey) *(e.g.*, /Employee(22)).

You can also delete the entities in an entity set, by calling [`$entityset/{entitySetID}`]($entityset.md#entitysetentitysetid).

## Example
You can then write the following REST request to delete the entity whose key is 22:

 `POST  /rest/Employee(22)/?$method=delete`

You can also do a query as well using $filter:

 `POST  /rest/Employee?$filter="ID=11"&$method=delete`

You can also delete an entity set using $entityset/{entitySetID}:

 `POST  /rest/Employee/$entityset/73F46BE3A0734EAA9A33CA8B14433570?$method=delete`

Response:

```
{
    "ok": true
}
```



## $method=entityset

Creates an entity set in 4D Server's cache based on the collection of entities defined in the REST request

### Description

When you create a collection of entities in REST, you can also create an entity set that will be saved in 4D Server's cache. The entity set will have a reference number that you can pass to `$entityset/{entitySetID}` to access it. By default, it is valid for two hours; however, you can modify that amount of time by passing a value (in seconds) to $timeout.

If you have used `$savedfilter` and/or `$savedorderby` (in conjunction with `$filter` and/or `$orderby`) when you created your entity set, you can recreate it with the same reference ID even if it has been removed from 4D Server's cache.

### Example

To create an entity set, which will be saved in 4D Server's cache for two hours, add `$method=entityset` at the end of your REST request:

 `GET  /rest/People/?$filter="ID>320"&$method=entityset`

You can create an entity set that will be stored in 4D Server's cache for only ten minutes by passing a new timeout to `$timeout`:

 `GET  /rest/People/?$filter="ID>320"&$method=entityset&$timeout=600`

You can also save the filter and order by, by passing true to `$savedfilter` and `$savedorderby`.
> `$skip` and `$top/$limit` are not taken into consideration when saving an entity set.

After you create an entity set, the first element, `__ENTITYSET`, is added to the object returned and indicates the URI to use to access the entity set:

`__ENTITYSET: "http://127.0.0.1:8081/rest/Employee/$entityset/9718A30BF61343C796345F3BE5B01CE7"`




## $method=release

Releases an existing entity set stored in 4D Server's cache.

### Description

You can release an entity set, which you created using [`$method=entityset`](#methodentityset), from 4D Server's cache.

### Example

Release an existing entity set:

`GET  /rest/Employee/$entityset/4C51204DD8184B65AC7D79F09A077F24?$method=release`

#### Response:

If the request was successful, the following response is returned:

```
{
    "ok": true
}
If the entity set wasn't found, an error is returned:

{
    "__ERROR": [
        {
             "message": "Error code: 1802\nEntitySet  \"4C51204DD8184B65AC7D79F09A077F24\" cannot be found\ncomponent:  'dbmg'\ntask 22, name: 'HTTP connection handler'\n",
            "componentSignature": "dbmg",
            "errCode": 1802
        }
    ]
}
```


## $method=subentityset

Creates an entity set in 4D Server's cache based on the collection of related entities defined in the REST request


### Description

`$method=subentityset` allows you to sort the data returned by the relation attribute defined in the REST request.

To sort the data, you use the `$subOrderby` property. For each attribute, you specify the order as ASC (or asc) for ascending order and DESC (desc) for descending order. By default, the data is sorted in ascending order.

If you want to specify multiple attributes, you can delimit them with a comma, µ, `$subOrderby="lastName desc, firstName asc"`.

### Example

If you want to retrieve only the related entities for a specific entity, you can make the following REST request where staff is the relation attribute in the Company dataclass linked to the Employee dataclass:

`GET  /rest/Company(1)/staff?$expand=staff&$method=subentityset&$subOrderby=lastName ASC`

#### Response:

```
{

    "__ENTITYSET": "/rest/Employee/$entityset/FF625844008E430B9862E5FD41C741AB",
    "__entityModel": "Employee",
    "__COUNT": 2,
    "__SENT": 2,
    "__FIRST": 0,
    "__ENTITIES": [
        {
            "__KEY": "4",
            "__STAMP": 1,
            "ID": 4,
            "firstName": "Linda",
            "lastName": "Jones",
            "birthday": "1970-10-05T14:23:00Z",
            "employer": {
                "__deferred": {
                    "uri": "/rest/Company(1)",
                    "__KEY": "1"
                }
            }
        },
        {
            "__KEY": "1",
            "__STAMP": 3,
            "ID": 1,
            "firstName": "John",
            "lastName": "Smith",
            "birthday": "1985-11-01T15:23:00Z",
            "employer": {
                "__deferred": {
                    "uri": "/rest/Company(1)",
                    "__KEY": "1"
                }
            }
        }
    ]

}
```


## $method=update


Updates and/or creates one or more entities

### Description

`$method=update` allows you to update and/or create one or more entities in a single **POST**. If you update and/or create one entity, it is done in an object with each property an attribute with its value, *e.g.*, `{ lastName: "Smith" }`. If you update and/or create multiple entities, you must create an array of objects.

To update an entity, you must pass the `__KEY` and `__STAMP` parameters in the object along with any modified attributes. If both of these parameters are missing, an entity will be added with the values in the object you send in the body of your **POST**.

All triggers, calculated attributes, and events are executed immediately when saving the entity to the server. The response contains all the data as it exists on the server.

You can also put these requests to create or update entities in a transaction by calling `$atomic/$atonce`. If any errors occur during data validation, none of the entities are saved. You can also use $method=validate to validate the entities before creating or updating them.

If a problem arises while adding or modifying an entity, an error will be returned to you with that information.
> Notes for specific attribute types:
> 
> * **Dates** must be expressed in JS format: YYYY-MM-DDTHH:MM:SSZ (e.g., "2010-10-05T23:00:00Z"). If you have selected the Date only property for your Date attribute, the time zone and time (hour, minutes, and seconds) will be removed. In this case, you can also send the date in the format that it is returned to you dd!mm!yyyy (e.g., 05!10!2013).
> * **Booleans** are either true or false.
> * Uploaded files using `$upload` can be applied to an attribute of type Image or BLOB by passing the object returned in the following format { "ID": "D507BC03E613487E9B4C2F6A0512FE50"}

### Example

To update a specific entity, you use the following URL:

 `POST  /rest/Person/?$method=update`

**POST data:**

```
{
    __KEY: "340",
    __STAMP: 2,
    firstName: "Pete",
    lastName: "Miller"
}
```

The firstName and lastName attributes in the entity indicated above will be modified leaving all other attributes (except calculated ones based on these attributes) unchanged.

If you want to create an entity, you can POST the attributes using this URL:

 `POST  /rest/Person/?$method=update`

**POST data:**

```
{ 
    firstName: "John",
    lastName: "Smith"
}
```

You can also create and update multiple entities at the same time using the same URL above by passing multiple objects in an array to the POST:

 `POST  /rest/Person/?$method=update`

**POST data:**

```
[{ 
    "__KEY": "309",
    "__STAMP": 5,
    "ID": "309",
    "firstName": "Penelope",
    "lastName": "Miller"
}, { 
    "firstName": "Ann",
    "lastName": "Jones"
}]
```

**Response:**

When you add or modify an entity, it is returned to you with the attributes that were modified. For example, if you create the new employee above, the following will be returned:

```
{
    "__KEY": "622", 
    "__STAMP": 1, 
    "uri": "http://127.0.0.1:8081/rest/Employee(622)", 
    "ID": 622, 
    "firstName": "John", 
    "firstName": "Smith",
    "fullName": "John Smith"
}
```
> The only reason the fullName attribute is returned is because it is a calculated attribute based on both firstName and lastName.

If, for example, the stamp is not correct, the following error is returned:

```
{
    "__ENTITIES": [
        {
            "__KEY": "309", 
            "__STAMP": 1, 
            "ID": 309, 
            "firstName": "Betty", 
            "lastName": "Smith", 
            "fullName": "Betty Smith", 
            "__ERROR": [
                {
                    "message": "Given stamp does not match current one for record# 308 of table Employee", 
                    "componentSignature": "dbmg", 
                    "errCode": 1263
                }, 
                {
                    "message": "Cannot save record 308 in table Employee of database Widgets", 
                    "componentSignature": "dbmg", 
                    "errCode": 1046
                }, 
                {
                    "message": "The entity# 308 of the datastore class \"Employee\" cannot be saved", 
                    "componentSignature": "dbmg", 
                    "errCode": 1517
                }
            ]
        }, 
        {
            "__KEY": "612", 
            "__STAMP": 4, 
            "uri": "http://127.0.0.1:8081/rest/Employee(612)", 
            "ID": 612, 
            "firstName": "Ann", 
            "lastName": "Jones", 
            "fullName": "Ann Jones"
        }
    ]
}
```

If, for example, the user does not have the appropriate permissions to update an entity, the following error is returned:

```
{
    "__KEY": "2", 
    "__STAMP": 4, 
    "ID": 2, 
    "firstName": "Paula", 
    "lastName": "Miller", 
    "fullName": "Paula Miller", 
    "telephone": "408-555-5555", 
    "salary": 56000, 
    "employerName": "Adobe", 
    "employer": {
        "__deferred": {
            "uri": "http://127.0.0.1:8081/rest/Company(1)", 
            "__KEY": "1"
        }
    }, 
    "__ERROR": [
        {
            "message": "No permission to update for dataClass Employee", 
            "componentSignature": "dbmg", 
            "errCode": 1558
        }, 
        {
            "message": "The entity# 1 of the datastore class \"Employee\" cannot be saved", 
            "componentSignature": "dbmg", 
            "errCode": 1517
        }
    ]
}
```


## $method=validate

Validates the request when adding and/or modifying entities

### Description

Before actually saving a new or modified entity with `$method=update`, you can first try to validate the actions with `$method=validate`.

### Example

In this example, we **POST** the following request to $method=validate:

 `POST  /rest/Employee/?$method=validate`

**POST data**:

```
[{
    "__KEY": "1",
    "__STAMP": 8,
    "firstName": "Pete",
    "lastName": "Jones",
    "salary": 75000
}, {
    "firstName": "Betty",
    "lastName": "Miller",
}]
```

**Response**:

If the request is successful, the following response is returned:

```
{
    "ok": true
}
```

Otherwise, you receive an error. In our case, we got an error because our salary field must be inferior to 60000:

```
{
    "__ENTITIES": [
        {
            "__ERROR": [
                {
                    "message": "Value cannot be greater than 60000", 
                    "componentSignature": "dbmg", 
                    "errCode": 1569
                }, 
                {
                    "message": "Entity fails validation", 
                    "componentSignature": "dbmg", 
                    "errCode": 1570
                }, 
                {
                    "message": "The new entity of the datastore class \"Employee\" cannot be saved", 
                    "componentSignature": "dbmg", 
                    "errCode": 1534
                }
            ]
        }
    ]
}
```