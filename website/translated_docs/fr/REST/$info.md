---
id: info
title: '$info'
---

Returns information about the entity sets currently stored in 4D Server's cache as well as user sessions

## Description
When you call this request for your project, you retrieve information in the following properties:

| Property       | Type    | Description                                                                                    |
| -------------- | ------- | ---------------------------------------------------------------------------------------------- |
| cacheSize      | Nombre  | Wakanda Server's cache size.                                                                   |
| usedCache      | Nombre  | How much of Wakanda Server's cache has been used.                                              |
| entitySetCount | Nombre  | Number of entity sets.                                                                         |
| entitySet      | Tableau | An array in which each object contains information about each entity set.                      |
| ProgressInfo   | Tableau | An array containing information about progress indicator information.                          |
| sessionInfo    | Tableau | An array in which each object contains information about each user session.                    |
| jsContextInfo  | Tableau | An array containing one object that returns the information about the JavaScript context pool. |

### entitySet
For each entity set currently stored in 4D Server's cache, the following information is returned:


| Property      | Type    | Description                                                                                                                                                                                                                                                         |
| ------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id            | Chaine  | A UUID that references the entity set.                                                                                                                                                                                                                              |
| tableName     | Chaine  | Name of the datastore class.                                                                                                                                                                                                                                        |
| selectionSize | Nombre  | Number of entities in the entity set.                                                                                                                                                                                                                               |
| sorted        | Booléen | Returns true if the set was sorted (using `$orderby`) or false if it's not sorted.                                                                                                                                                                                  |
| refreshed     | Date    | When the entity set was created or the last time it was used.                                                                                                                                                                                                       |
| expires       | Date    | When the entity set will expire (this date/time changes each time when the entity set is refreshed). The difference between refreshed and expires is the timeout for an entity set. This value is either two hours by default or what you defined using `$timeout`. |

For information about how to create an entity set, refer to `$method=entityset`. If you want to remove the entity set from 4D Server's cache, use `$method=release`.
> 4D also creates its own entity sets for optimization purposes, so the ones you create with `$method=entityset` are not the only ones returned.
> **IMPORTANT** If your project is in **Controlled Admin Access Mode**, you must first log into the project as a user in the Admin group.

### sessionInfo

For each user session, the following information is returned in the *sessionInfo* array:

| Property   | Type   | Description                                                  |
| ---------- | ------ | ------------------------------------------------------------ |
| sessionID  | Chaine | A UUID that references the session.                          |
| userID     | Chaine | A UUID that references the user who runs the session.        |
| userName   | Chaine | The name of the user who runs the session.                   |
| lifeTime   | Nombre | The lifetime of a user session in seconds (3600 by default). |
| expiration | Date   | The current expiration date and time of the user session.    |

### jsContextInfo

The object in the **jsContextInfo** array details the JavaScript context pool:


| Property              | Type    | Description                                                                           |
| --------------------- | ------- | ------------------------------------------------------------------------------------- |
| contextPoolSize       | Nombre  | Maximum number of reusable contexts that can be stored in the JS pool (50 by default) |
| activeDebugger        | Booléen | Debugger state (false by default)                                                     |
| usedContextCount      | Nombre  | Number of used contexts                                                               |
| usedContextMaxCount   | Nombre  | Maximum number of contexts that have been used simultaneously                         |
| reusableContextCount  | Nombre  | Number of reusable contexts (both used and unused)                                    |
| unusedContextCount    | Nombre  | Number of unused contexts                                                             |
| createdContextCount   | Nombre  | Number of contexts created since the project was started                              |
| destroyedContextCount | Nombre  | Number of contexts destroyed since the project was started                            |

## Exemple

Retrieve information about the entity sets currently stored in 4D Server's cache as well as user sessions:

`GET /rest/$info`

**Result**:

```
{
cacheSize: 209715200,
usedCache: 3136000,
entitySetCount: 4,
entitySet: [
    {
        id: "1418741678864021B56F8C6D77F2FC06",
        tableName: "Company",
        selectionSize: 1,
        sorted: false,
        refreshed: "2011-11-18T10:30:30Z",
        expires: "2011-11-18T10:35:30Z"
    },
    {
        id: "CAD79E5BF339462E85DA613754C05CC0",
        tableName: "People",
        selectionSize: 49,
        sorted: true,
        refreshed: "2011-11-18T10:28:43Z",
        expires: "2011-11-18T10:38:43Z"
    },
    {
        id: "F4514C59D6B642099764C15D2BF51624",
        tableName: "People",
        selectionSize: 37,
        sorted: false,
        refreshed: "2011-11-18T10:24:24Z",
        expires: "2011-11-18T12:24:24Z"
    }
],
ProgressInfo: [
    {
        UserInfo: "flushProgressIndicator",
        sessions: 0,
        percent: 0
    },
    {
        UserInfo: "indexProgressIndicator",
        sessions: 0,
        percent: 0
    }
],
sessionInfo: [ 
    {
        sessionID: "6657ABBCEE7C3B4089C20D8995851E30",
        userID: "36713176D42DB045B01B8E650E8FA9C6",
        userName: "james",
        lifeTime: 3600,
        expiration: "2013-04-22T12:45:08Z"
    },
    {
        sessionID: "A85F253EDE90CA458940337BE2939F6F",
        userID: "00000000000000000000000000000000",
        userName: "default guest",
        lifeTime: 3600,
        expiration: "2013-04-23T10:30:25Z"
}
],
jsContextInfo: [
    {
        "contextPoolSize": 50,
        "activeDebugger": false,
        "usedContextCount": 1,
        "usedContextMaxCount": 1,
        "reusableContextCount": 1,
        "unusedContextCount": 0,
        "createdContextCount": 4,
        "destroyedContextCount": 3
    }
]
}
```
> The progress indicator information listed after the entity sets is used internally by 4D.