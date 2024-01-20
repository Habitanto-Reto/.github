# Domain Model

> Context and vocabulary

Many times the client or the applicant "does not know" what he/she is asking for, or simply has loose ideas that it is up to our side to solve, because it is a challenge, it is simply posed under the [same challenge ](https://github.com/luisphi/reto01_habi) of the domain model.

# Requirements

> Uses cases

### Auth (Users)

| Register | Login | Logout | View Profile |
| -------- | ----- | ------ | ------------ |

### Task (Users)

> TODO: view dataTable & UI/UX (using SSR)

| Create task | Asign Task | Edit Task (own) | Delete task ( own\|as complete?) | Toggle Complete Task |
| ----------- | ---------- | --------------- | -------------------------------- | -------------------- |

### Notifications (System)

| Notifies (mail) task creator when task is marked as complete |
| ------------------------------------------------------------ |

# Analysis

> Discovering classes and responsibilities

```mermaid
stateDiagram-v2
    [*] --> Controllers
    Controllers --> Models: Request data
    Models --> Services: Processes business logic
    Services --> Repository: Accesses data
    Repository --> DataSources: Interacts with data sources
    DataSources --> [*]: End of flow
```



# Desing

> Designing classes and responsibilities

## Auth

```mermaid
---
title: Autenticación
---
classDiagram
    class UserController {
        +registerUser(user : User)
        +loginUser(email : string, password : string)
        +viewProfile(userId : UUID)
    }
    class UserService {
        -repository : UserRepository
        +createUser(user : User)
        +authenticateUser(email : string, password : string)
        +getUserProfile(userId : UUID)
    }
    class UserRepository {
        -dataSource : UserDataSourcea
        +addUser(user : User)
        +findUserByEmail(email : string)
        +getUserById(userId : UUID)
    }
    class UserDataSource {
        +saveUser(user : User)
        +getUserByEmail(email : string)
        +getUserById(userId : UUID)
    }
    class User {
        -uuid : UUID
        -email : string
        -name : string
        -password : string
    }
    UserController --> UserService : uses
    UserService --> UserRepository : <<inject>>
    UserRepository --> UserDataSource : <<inject>>
    User <-- UserController : data
    User <-- UserService : data
    User <-- UserRepository : data
    User <-- UserDataSource : data
```



## Task

```mermaid
---
title: Task
---
classDiagram
    class TaskController {
        +createTask(task : Task)
        +assignTask(taskId : UUID, userId : UUID)
        +editTask(task : Task)
        +deleteTask(taskId : UUID)
        +completeTask(taskId : UUID)
        +listTasks(filter : TaskFilter)
        }
    class TaskService {
        -repository : TaskRepository
        -kafkaProducer : KafkaProducer
        +createTask(task : Task)
        +assignTask(taskId : UUID, userId : UUID)
        +editTask(task : Task)+deleteTask(taskId : UUID)
        +completeTask(taskId : UUID)
        +listTasks(filter : TaskFilter)
        }
    
    class TaskRepository {
        -dataSource : TaskDataSource
        +addTask(task : Task)
        +updateTask(task : Task)
        +deleteTask(taskId : UUID)
        +findTasks(filter : TaskFilter)
        }

    class TaskDataSource {
        +saveTask(task : Task)
        +updateTask(task : Task)
        +removeTask(taskId : UUID)
        +getTasks(filter : TaskFilter)    
    }

    class Task {
        -title : string
        -creatorUserId : UUID
        -assigneeUserId : UUID
        -isCompleted : bool
        -createdDate : DateTime
        -completedDate : DateTime    
        }

    class KafkaProducer {
        +produceTaskCompletedEvent(taskId : UUID, userId : UUID, userEmail : string)
        }

    class TaskFilter {
        -pageNumber : int
        -pageSize : int-name : string
        -email : string
        }

    TaskController --> TaskService : uses
    TaskService --> TaskRepository : <<inject>>
    TaskRepository --> TaskDataSource : <<inject>>
    TaskService --> KafkaProducer : uses
    Task <-- TaskController : data
    Task <-- TaskService : data
    Task <-- TaskRepository : data
    Task <-- TaskDataSource : data
```



## Notification

```mermaid
---
title: Notification
---
classDiagram
    class TaskCompletedListenerController {
        +onTaskCompleted(event : )
        }

    class TaskCompletedService {
        -externalEmailService : ExternalEmailService
        +processCompletedTask(event : )
    }

    class  TaskCompletedEvent{
        -taskId : UUID
        -userId : UUID
        -userEmail : string
        }

    class ExternalEmailService {
        +sendEmail(taskId : UUID, userEmail : string)
        }

    TaskCompletedListenerController --> TaskCompletedService : uses
    TaskCompletedService --> ExternalEmailService : uses
    TaskCompletedEvent <-- TaskCompletedListenerController : data
    TaskCompletedEvent <-- TaskCompletedService : data

```



# Implementation

> Code

# Testing

# Deploy

# Tools

Specification commit messages: [Conventionalcommits](https://www.conventionalcommits.org/en/v1.0.0/) & [Gitmoji](https://gitmoji.dev/)

# Mental draft 

![image](https://github.com/Habitanto-Reto/.github/assets/53282017/d1eaec02-6cde-4676-aa4f-72885874111e)
