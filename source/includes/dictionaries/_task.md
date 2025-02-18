## Task

Using the JSON API, you can create and update information about tasks, request lists of tasks, and information on individual tasks. The entity code for a task in the JSON API is the **task** keyword. Learn more about [Tasks](https://kladana.zendesk.com/hc/en-us/articles/4402929059985-Tasks).

### Tasks
#### Entity attributes

| Title | Type                                               | Filtration | Description |
| ------ |----------------------------------------------------| ----- | ------- |
| **accountId** | UUID                                               | `=` `!=` | Cashier account ID<br>`+Required when replying` `+Read only` |
| **agent** | [Meta](../#kladana-json-api-general-info-metadata) | `=` `!=` | Metadata of the Account or legal entity associated with the task. A task can be linked either to a counterparty, or to a legal entity, or to a document<br>`+Expand` |
| **assignee** | [Meta](../#kladana-json-api-general-info-metadata) | `=` `!=` | Task owner metadata<br>`+Required when replying` `+Expand` `+Required when creating` |
| **author** | [Meta](../#kladana-json-api-general-info-metadata) | `=` `!=` | Metadata of the Employee who created the task (account administrator, if the author is an Application)<br>`+Required when replying` `+Read-only` `+Expand` |
| **authorApplication** | [Meta](../#kladana-json-api-general-info-metadata) | | Metadata of the Application that created the task<br>`+Read Only` `+Expand` |
| **completed** | DateTime                                           | | Task execution time<br>`+Required for response` `+Read-only` |
| **created** | DateTime                                           | `=` `!=` `<` `>` `<=` `>=` | Creation time<br>`+Required when replying` `+Read only` |
| **description** | String(4096)                                       | `=` `!=` `~` `~=` `=~` | Task text<br>`+Required when replying` `+Required when creating` |
| **done** | Boolean                                            | `=` `!=` | Task completion mark<br>`+Required when answering` |
| **dueToDate** | DateTime                                           | `=` `!=` `<` `>` `<=` `>=` | Task deadline |
| **files** | MetaArray                                          | | [Files](../dictionaries/#entities-files) array metadata (Maximum number of files - 100)<br>`+Required when replying` `+Expand` |
| **id** | UUID                                               | `=` `!=` | Task ID<br>`+Required when replying` `+Read Only` |
| **implementer** | [Meta](../#kladana-json-api-general-info-metadata) | | Metadata of the Employee who completed the task<br>`+Read-only` `+Expand` |
| **meta** | [Meta](../#kladana-json-api-general-info-metadata) | | Task Metadata<br>`+Required when answering` |
| **notes** | [Meta](../#kladana-json-api-general-info-metadata) | | Task comment metadata<br>`+Required when replying` `+Expand` |
| **operation** | [Meta](../#kladana-json-api-general-info-metadata) | `=` `!=` | Metadata of the Document associated with the issue. A task can be linked either to a counterparty, or to a legal entity, or to a document<br>`+Expand` |
| **updated** | DateTime                                           | `=` `!=` `<` `>` `<=` `>=` | Last updated time Tasks<br>`+Required when replying` `+Read-only` |

#### Task comments
The task comment object contains the following fields:

| Title | Type                                               | Description |
| ------ |----------------------------------------------------|------- |
| **author** | [Meta](../#kladana-json-api-general-info-metadata) | Metadata of the Person who created the comment (account administrator if the author is an app)<br>`+Required when replying` `+Read Only` |
| **authorApplication** | [Meta](../#kladana-json-api-general-info-metadata) | Metadata of the Application that created the comment<br>`+Read Only` |
| **moment** | DateTime                                           | When the comment was created<br>`+Required when replying` `+Read only` |
| **description** | String(4096)                                       | Comment text<br>`+Required when replying` `+Required when creating` |

#### Default list display
##### For administrator
If the current employee has administrator rights, then when requesting a list of tasks, all active (**done** = false) tasks will be displayed to him, like those
  that relate to him (the employee is the creator or performer of the task), and those that relate to other employees.
 
##### For employee
For an employee who is not an administrator, but has the right to view all tasks, the list of tasks by default will be similar to the list of tasks displayed for the administrator. Otherwise, when requesting a list of tasks without any filters, active (**done** = false) tasks created by the current employee and tasks for which the current employee is responsible will be displayed.

#### Filters from the web interface
There are 2 groups of filters in the main interface of MySklad to display the list of tasks:

+ Filter by relationship with the current employee: `Assigned to me`, `I assigned`, `All tasks` (only displayed for administrators)
+ Filter by task readiness: `Active`, `Completed`.

To implement similar list filtering for the JSON API, you need to use the following filters for the task list:

+ **Assigned to me**: filter by the **assignee** field, the value of which contains a link to the current employee<br>
`https://app.kladana.in/api/remap/1.2/entity/task?filter=assignee=https://app.kladana.in/api/remap/1.2/entity/employee/<current employee id> `
+ **I instructed**: filter by the field **author** whose value contains a link to the current employee<br>
`https://app.kladana.in/api/remap/1.2/entity/task?filter=author=https://app.kladana.in/api/remap/1.2/entity/employee/<current employee id> `
+ **All tasks**: does not require filtering. Pay attention to the item [Default list display](../dictionaries/#entities-task-tasks-default-list-display)
+ **Active**: filter by field **done** with value false<br>
`https://app.kladana.in/api/remap/1.2/entity/task?filter=done=false`
+ **Done**: filter by field **done** with value true<br>
`https://app.kladana.in/api/remap/1.2/entity/task?filter=done=true`

#### Permissions
| Operation | Access |
| ------ | ------- |
| Task completion | tariff option required CRM, administrator, task creator, responsible |
| Cancel task execution | tariff option required CRM, administrator, task creator, responsible |
| View tasks in which the current user is responsible or author | All |
| View any tasks| user with view all tasks or administrator |
| Editing a task | tariff option required CRM, administrator, task creator |
| Create a task | tariff option required CRM, all |
| Deleting a task | Administrator, task creator |



### Get tasks
Get a list of tasks.
Result: JSON object including fields:

| Title | Type | Description |
| ------ | ------- |------ |
| **meta** | [Meta](../#kladana-json-api-general-info-metadata) | Issuance metadata, |
| **context** | [Meta](../#kladana-json-api-general-info-metadata) | Metadata about the person who made the request. |
| **rows** | Array(Object) | An array of JSON objects representing [Tasks](../dictionaries/#entities-task). |

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **limit** | `number` (optional) **Default: 1000** *Example: 1000* The maximum number of entities to retrieve. `Allowed values are 1 - 1000`. |
| **offset** | `number` (optional) **Default: 0** *Example: 40* Indent in the output list of entities. |

> Get tasks

```shell
curl -X GET
   "https://app.kladana.in/api/remap/1.2/entity/task"
   -H "Authorization: Basic <Credentials>"
```

> Response 200(application/json)
Successful request. The result is a JSON representation of the task list.

```json
{
   "context": {
     "employee": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/context/employee",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json"
       }
     }
   },
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task",
     "type": "task",
     "mediaType": "application/json",
     "size": 2,
     "limit": 1000,
     "offset": 0
   },
   "rows": [
     {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
         "type": "task",
         "mediaType": "application/json"
       },
       "id": "47bc1d9b-8b87-11e8-d9ce-84d900000017",
       "accountId": "98182760-8aa1-11e8-7210-075e00000001",
       "author": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
           "type": "employee",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
         }
       },
       "created": "2018-07-19 22:09:37",
       "updated": "2018-07-19 22:10:01",
       "description": "task3",
       "dueToDate": "2018-07-20 22:09:00",
       "assignee": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
           "type": "employee",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
         }
       },
       "done": false,
       "agent": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/counterparty/992b6965-8aa1-11e8-7210-075e00000057",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/counterparty/metadata",
           "type": "counterparty",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#company/edit?id=992b6965-8aa1-11e8-7210-075e00000057"
         }
       },
       "notes": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes",
           "type": "tasknote",
           "mediaType": "application/json",
           "size": 2,
           "limit": 1000,
           "offset": 0
         }
       }
     },
     {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/task/aaca57a2-8b86-11e8-d9ce-84d900000007",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
         "type": "task",
         "mediaType": "application/json"
       },
       "id": "aaca57a2-8b86-11e8-d9ce-84d900000007",
       "accountId": "98182760-8aa1-11e8-7210-075e00000001",
       "author": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
           "type": "employee",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
         }
       },
       "created": "2018-07-19 22:05:14",
       "updated": "2018-07-19 22:09:42",
       "description": "task1",
       "dueToDate": "2018-07-20 22:05:00",
       "assignee": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
           "type": "employee",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
         }
       },
       "done": false,
       "operation": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/supply/7eb5b552-8aa3-11e8-7210-075e000000f1",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/supply/metadata",
           "type": "supply",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#supply/edit?id=7eb5b552-8aa3-11e8-7210-075e000000f1"
         }
       },
       "notes": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/task/aaca57a2-8b86-11e8-d9ce-84d900000007/notes",
           "type": "tasknote",
           "mediaType": "application/json",
           "size": 0,
           "limit": 1000,
           "offset": 0
         }
       }
     }
   ]
}
```

### Create Task
Create a new task. To create new tasks, you need an active tariff option.

Mandatory fields to create:


| Title | Type | Description |
| ------ | ------- |----- |
| **description** | String(4096) | Task text<br>`+Required when replying` `+Required when creating` |
| **assignee** | [Meta](../#kladana-json-api-general-info-metadata) | Metadata of the Employee responsible for the task<br>`+Required when replying` `+Expand` `+Required when creating` |

> An example of a request to create a new task.

```shell
   curl -X POST
     "https://app.kladana.in/api/remap/1.2/entity/task"
     -H "Authorization: Basic <Credentials>"
     -H "Content-Type: application/json"
       -d '{
             "description": "Correct details of a legal entity",
             "dueToDate": "2017-05-12 17:12:00",
             "assignee": {
               "meta": {
                 "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
                 "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
                 "type": "employee",
                 "mediaType": "application/json"
               }
             },
             "done": false,
             "agent": {
               "meta": {
                 "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
                 "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
                 "type": "organization",
                 "mediaType": "application/json"
               }
             }
           }'
```

> Response 200(application/json)
Successful request. The result is a JSON representation of the created task.

```json
{
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task/cfe5a6ae-8b87-11e8-d9ce-84d900000000",
     "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
     "type": "task",
     "mediaType": "application/json"
   },
   "id": "cfe5a6ae-8b87-11e8-d9ce-84d900000000",
   "accountId": "98182760-8aa1-11e8-7210-075e00000001",
   "author": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "created": "2018-07-19 22:13:25",
   "updated": "2018-07-19 22:13:25",
   "description": "Correct details of a legal entity",
   "dueToDate": "2017-05-12 17:12:00",
   "assignee": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "done": false,
   "agent": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
       "type": "organization",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#mycompany/edit?id=9927bf0d-8aa1-11e8-7210-075e00000054"
     }
   },
   "notes": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/cfe5a6ae-8b87-11e8-d9ce-84d900000000/notes",
       "type": "tasknote",
       "mediaType": "application/json",
       "size": 0,
       "limit": 1000,
       "offset": 0
     }
   }
}
```

### Bulk creation and updating of Tasks
[Bulk creation and update of Tasks](../#kladana-json-api-general-info-create-and-update-multiple-objects). In the body of the request, you need to pass an array containing the JSON representation of the Issues that you want to create or update.
Updated Tasks must contain the identifier in the form of metadata.

> Example of creating and updating multiple Tasks

```shell
   curl -X POST
     "https://app.kladana.in/api/remap/1.2/entity/task"
     -H "Authorization: Basic <Credentials>"
     -H "Content-Type: application/json"
       -d'[
             {
               "description": "Correct details of a legal entity",
               "dueToDate": "2017-05-12 17:12:00",
               "assignee": {
                 "meta": {
                   "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
                   "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
                   "type": "employee",
                   "mediaType": "application/json"
                 }
               },
               "done": false,
               "agent": {
                 "meta": {
                   "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
                   "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
                   "type": "organization",
                   "mediaType": "application/json"
                 }
               }
             },
             {
               "meta": {
                 "href": "https://app.kladana.in/api/remap/1.2/entity/task/cfe5a6ae-8b87-11e8-d9ce-84d900000000",
                 "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
                 "type": "task",
                 "mediaType": "application/json"
               },
               "description": "Specify contact persons",
               "assignee": {
                 "meta": {
                   "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
                   "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
                   "type": "employee",
                   "mediaType": "application/json"
                 }
               },
               "done": true,
               "agent": {
                 "meta": {
                   "href": "https://app.kladana.in/api/remap/1.2/entity/counterparty/992b6965-8aa1-11e8-7210-075e00000057",
                   "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/counterparty/metadata",
                   "type": "counterparty",
                   "mediaType": "application/json"
                 }
               }
             }
           ]'
```

> Response 200(application/json)
Successful request. The result is a JSON array of representations of the created and updated Issues.

```json
[
   {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/9971ba00-8b88-11e8-d9ce-84d900000009",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
       "type": "task",
       "mediaType": "application/json"
     },"id": "9971ba00-8b88-11e8-d9ce-84d900000009",
     "accountId": "98182760-8aa1-11e8-7210-075e00000001",
     "author": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "created": "2018-07-19 22:19:04",
     "updated": "2018-07-19 22:19:04",
     "description": "Correct details of a legal entity",
     "dueToDate": "2017-05-12 17:12:00",
     "assignee": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "done": false,
     "agent": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
         "type": "organization",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#mycompany/edit?id=9927bf0d-8aa1-11e8-7210-075e00000054"
       }
     },
     "notes": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/task/9971ba00-8b88-11e8-d9ce-84d900000009/notes",
         "type": "tasknote",
         "mediaType": "application/json",
         "size": 0,
         "limit": 1000,
         "offset": 0
       }
     }
   },
   {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/cfe5a6ae-8b87-11e8-d9ce-84d900000000",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
       "type": "task",
       "mediaType": "application/json"
     },
     "id": "cfe5a6ae-8b87-11e8-d9ce-84d900000000",
     "accountId": "98182760-8aa1-11e8-7210-075e00000001",
     "author": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "created": "2018-07-19 22:13:25",
     "updated": "2018-07-19 22:19:04",
     "description": "Specify contact persons",
     "dueToDate": "2017-05-12 17:12:00",
     "implementer": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "completed": "2018-07-19 22:19:04",
     "assignee": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "done": true,
     "agent": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/counterparty/992b6965-8aa1-11e8-7210-075e00000057",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/counterparty/metadata",
         "type": "counterparty",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#company/edit?id=992b6965-8aa1-11e8-7210-075e00000057"
       }
     },
     "notes": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/task/cfe5a6ae-8b87-11e8-d9ce-84d900000000/notes",
         "type": "tasknote",
         "mediaType": "application/json",
         "size": 0,
         "limit": 1000,
         "offset": 0
       }
     }
   }
]
```

### Delete task

Request to delete a task with the specified id. To delete tasks, you need an active tariff option.

Also, you cannot delete tasks created by other employees without administrator rights.

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |

> Request to delete the task with the specified id.

```shell
curl -X DELETE
   "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19"
   -H "Authorization: Basic <Credentials>"
```

> Response 200(application/json)
Successfully deleting the task.

### Bulk deletion of Tasks

In the body of the request, you need to pass an array containing the JSON metadata of the Tasks that you want to delete.


> Request for bulk deletion of Tasks.

```shell
curl -X POST
   "https://app.kladana.in/api/remap/1.2/entity/task/delete"
   -H "Authorization: Basic <Credentials>"
   -H "Content-Type: application/json"
   -d'[
        {
            "meta": {
                "href": "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b1",
                "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
                "type": "task",
                "mediaType": "application/json"
            }
        },
        {
            "meta": {
                "href": "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b2",
                "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
                "type": "task",
                "mediaType": "application/json"
            }
        }
      ]'
```

> Successful request. Result - JSON information about deleting Tasks.

```json
[
   {
     "info":"Entity 'task' with UUID: 7944ef04-f831-11e5-7a69-971500188b1 successfully deleted"
   },
   {
     "info":"Entity 'task' with UUID: 7944ef04-f831-11e5-7a69-971500188b2 successfully deleted"
   }
]
```

### Task

### Get task

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |
 
> Request to get a separate task with the specified id.

```shell
curl -X GET
   "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19"
   -H "Authorization: Basic <Credentials>"
```

> Response 200(application/json)
Successful request. The result is a JSON representation of the task with the specified id.

```json
{
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017",
     "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
     "type": "task",
     "mediaType": "application/json"
   },
   "id": "47bc1d9b-8b87-11e8-d9ce-84d900000017",
   "accountId": "98182760-8aa1-11e8-7210-075e00000001",
   "author": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "created": "2018-07-19 22:09:37",
   "updated": "2018-07-19 22:13:10",
   "description": "task3",
   "dueToDate": "2018-07-20 22:09:00",
   "assignee": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "done": false,
   "agent": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
       "type": "organization",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#mycompany/edit?id=9927bf0d-8aa1-11e8-7210-075e00000054"
     }
   },
   "notes": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes",
       "type": "tasknote",
       "mediaType": "application/json",
       "size": 2,
       "limit": 1000,
       "offset": 0
     }
   }
}
```

### Edit task
#### Description
Edit the task with the specified id. To change tasks, you need an active tariff option.

Also, you cannot modify tasks created by other employees without administrator rights.

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |

> An example of a request to update an existing task.

```shell
   curl -X PUT
     "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19"
     -H "Authorization: Basic <Credentials>"
     -H "Content-Type: application/json"
       -d '{
             "description": "Specify contact persons",
             "assignee": {
               "meta": {
                 "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
                 "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
                 "type": "employee",
                 "mediaType": "application/json",
                 "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
               }
             },
             "done": true,
             "agent": {
               "meta": {
                 "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
                 "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
                 "type": "organization",
                 "mediaType": "application/json",
                 "uuidHref": "https://app.kladana.in/app/#mycompany/edit?id=9927bf0d-8aa1-11e8-7210-075e00000054"
               }
             }
           }'
```

> Response 200(application/json)
Successful request. The result is a JSON representation of the updated task.

```json
{
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017",
     "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/task/metadata",
     "type": "task",
     "mediaType": "application/json"
   },
   "id": "47bc1d9b-8b87-11e8-d9ce-84d900000017",
   "accountId": "98182760-8aa1-11e8-7210-075e00000001",
   "author": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "created": "2018-07-19 22:09:37",
   "updated": "2018-07-19 22:24:50",
   "description": "Specify contact persons",
   "dueToDate": "2018-07-20 22:09:00",
   "implementer": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "completed": "2018-07-19 22:24:24",
   "assignee": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "done": true,
   "agent": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/organization/9927bf0d-8aa1-11e8-7210-075e00000054",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/organization/metadata",
       "type": "organization",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#mycompany/edit?id=9927bf0d-8aa1-11e8-7210-075e00000054"
     }
   },
   "notes": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes",
       "type": "tasknote",
       "mediaType": "application/json",
       "size": 2,
       "limit": 1000,
       "offset": 0
     }
   }
}
```

### Comments Tasks
A separate resource for managing Task comments. With it, you can manage the comments of an issue where the number of comments exceeds the limit on the number of comments that can be saved with the issue. This limit is 1000.

### Get comments Tasks
Request to get a list of all comments for this Issue.

| Title | Type | Description |
| ------ | ------- | ------- |
| **meta** | [Meta](../#kladana-json-api-general-info-metadata) | Issuance metadata, |
| **context** | [Meta](../#kladana-json-api-general-info-metadata) | Metadata about the person who made the request. |
| **rows** | Array(Object) | An array of JSON objects representing the comment totask. |

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |
| **limit** | `number` (optional) **Default: 25** *Example: 100* Maximum number of entities to retrieve. `Allowed values are 1 - 100`. |
| **offset** | `number` (optional) **Default: 0** *Example: 40* Indent in returned list of entities |
| **updatedBy** | `string` (optional) *Example: admin@admin* One of the [selection filter options](../#kladana-json-api-general-info-filtering-the-selection-using-the-filter-parameter ). String format : `uid` |
| **updatedFrom** | `string` (optional) *Example: 2016-04-15 15:48:46* One of the [selection filter options](../#kladana-json-api-general-info-filtering-the-selection-using-the-filter-parameter). String format : `YYYY-MM-DD HH:MM:SS[.mmm]`, Time zone: `MSK` (Moscow time) |
| **updatedTo** | `string` (optional) *Example: 2016-04-15 15:48:46* One of the [selection filter options](../#kladana-json-api-general-info-filtering-the-selection-using-the-filter-parameter). String format : `YYYY-MM-DD HH:MM:SS[.mmm]`, Time zone: `MSK` (Moscow time) |

> Get comments Tasks

```shell
curl -X GET
   "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19/notes"
   -H "Authorization: Basic <Credentials>"
```
 
> Response 200(application/json)
Successful request. The result is a JSON representation of a list of individual Task comments.

```json
{
   "context": {
     "employee": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/context/employee",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json"
       }
     }
   },
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes",
     "type": "tasknote",
     "mediaType": "application/json",
     "size": 2,
     "limit": 1000,
     "offset": 0
   },
   "rows": [
     {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/55eaa2cc-8b87-11e8-d9ce-84d900000025",
         "type": "tasknote",
         "mediaType": "application/json"
       },
       "author": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
           "type": "employee",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
         }
       },
       "text": "text of comment 1",
       "moment": "2018-07-19 22:10:01"
     },
     {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/55eaac82-8b87-11e8-d9ce-84d900000026",
         "type": "tasknote",
         "mediaType": "application/json"
       },
       "author": {
         "meta": {
           "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
           "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
           "type": "employee",
           "mediaType": "application/json",
           "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
         }
       },
       "text": "text of comment 2",
       "moment": "2018-07-19 22:10:01"
     }
   ]
}
```

### Create comment Tasks
Request to create a new comment to the Task.
For successful creation, the following fields must be specified in the request body:

| Title | Type | Description |
| ------ | ------- |------ |
| **text** | String(4096) | Comment text<br>`+Required when replying` `+Required when creating` |

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |

> An example of creating one comment to a Task.

```shell
   curl -X POST
     "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19/notes"
     -H "Authorization: Basic <Credentials>"
     -H "Content-Type: application/json"
       -d '{
             "text": "text of comment 3"
           }'
```

> Response 200(application/json)
Successful request. The result is a JSON representation of the created comment for a single Issue.

```json
[
   {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/5528751f-8b8a-11e8-d9ce-84d90000000f",
       "type": "tasknote",
       "mediaType": "application/json"
     },
     "author": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "text": "text of comment 3",
     "moment": "2018-07-19 22:31:28"
   }
]
```

> An example of creating several comments to a Task at once.

```shell
   curl -X POST
     "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19/notes"
     -H "Authorization: Basic <Credentials>"
     -H "Content-Type: application/json"
       -d'[
             {
               "text": "comment text 4"
             },
             {
               "text": "comment text 5"
             }
           ]'
```

> Response 200(application/json)
Successful request. The result is a JSON representation of a list of created comments for a single Issue.

```json
[
   {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/8ba69d28-8b8a-11e8-d9ce-84d900000012",
       "type": "tasknote",
       "mediaType": "application/json"
     },
     "author": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "text": "comment text 4",
     "moment": "2018-07-19 22:32:59"
   },
   {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/8ba6a80c-8b8a-11e8-d9ce-84d900000013",
       "type": "tasknote",
       "mediaType": "application/json"
     },
     "author": {
       "meta": {
         "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
         "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
         "type": "employee",
         "mediaType": "application/json",
         "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
       }
     },
     "text": "comment text 5",
     "moment": "2018-07-19 22:32:59"
   }
]
```

### Comment to the task

### Get a comment on a Task

Single comment to the Issue with the specified comment id.

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |
| **tasknoteID** | `string` (required) *Example: 34f6344f-015e-11e6-9464-e4de0000006c* Issue comment id. |
 
> Request for a separate comment to the Issue with the specified id.

```shell
curl -X GET
   "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19/notes/34f6344f-015e-11e6-9464-e4de0000006c"
   -H "Authorization: Basic <Credentials>"
```

> Response 200(application/json)
Successful request. The result is a JSON representation of a single comment on the Issue.

```json
{
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/55eaa2cc-8b87-11e8-d9ce-84d900000025",
     "type": "tasknote",
     "mediaType": "application/json"
   },
   "author": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "text": "text of comment 1",
   "moment": "2018-07-19 22:10:01"
}
```

### Edit comment to the Task
Request to update a separate comment to the Issue.
For successful creation, the following fields must be specified in the request body:

| Title | Type | Description |
| ------ | ------- |------- |
| **text** | String(4096) | Comment text<br>`+Required when replying` `+Required when creating` |

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |
| **tasknoteID** | `string` (required) *Example: 34f6344f-015e-11e6-9464-e4de0000006c* Issue comment id. |

> An example of a request to update a separate comment to a Task.

```shell
   curl -X PUT
     "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19/notes/34f6344f-015e-11e6-9464-e4de0000006c"
     -H "Authorization: Basic <Credentials>"
     -H "Content-Type: application/json"
       -d '{
             "text": "new comment text 1"
           }'
```

> Response 200(application/json)
Successful request. The result is a JSON representation of the updated issue comment.

```json
{
   "meta": {
     "href": "https://app.kladana.in/api/remap/1.2/entity/task/47bc1d9b-8b87-11e8-d9ce-84d900000017/notes/55eaa2cc-8b87-11e8-d9ce-84d900000025",
     "type": "tasknote",
     "mediaType": "application/json"
   },
   "author": {
     "meta": {
       "href": "https://app.kladana.in/api/remap/1.2/entity/employee/98fa7086-8aa1-11e8-7210-075e0000002c",
       "metadataHref": "https://app.kladana.in/api/remap/1.2/entity/employee/metadata",
       "type": "employee",
       "mediaType": "application/json",
       "uuidHref": "https://app.kladana.in/app/#employee/edit?id=98fa7086-8aa1-11e8-7210-075e0000002c"
     }
   },
   "text": "new comment text 1",
   "moment": "2018-07-19 22:10:01"
}
```

### Delete comment

**Parameters**

| Parameter | Description |
| ------ | ------- |
| **id** | `string` (required) *Example: 7944ef04-f831-11e5-7a69-971500188b19* task id. |
| **tasknoteID** | `string` (required) *Example: 34f6344f-015e-11e6-9464-e4de0000006c* Issue comment id. |
 
> Request to delete a separate comment to the Task with the specified id.

```shell
curl -X DELETE
   "https://app.kladana.in/api/remap/1.2/entity/task/7944ef04-f831-11e5-7a69-971500188b19/notes/34f6344f-015e-11e6-9464-e4de0000006c"
   -H "Authorization: Basic <Credentials>"
```

> Response 200(application/json)
Successful deletion of the comment to the Task.
