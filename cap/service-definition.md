---
title: Defining the Service
layout: home
nav_order: 4
---

# Objectives
- Learn how to define a CDS service to expose your data model
- Understand how to create projections on entities
- Learn about field annotations for controlling API behavior

# Wait, but why?
In CAP, a service defines the API that your application exposes. While the data model (in the `db` folder) defines the structure of your data, the service (in the `srv` folder) defines what data is exposed and how. This separation allows you to have a rich internal data model while exposing only what's needed through the API.

---

# Exercise

## 0 - Run the tests first

Before starting, open Bruno and navigate to the **Tests** folder. Right-click on the folder named **1. Defining the Service**, click on **Run** and select **Recursive Run**. You should see all tests fail — this is expected! By the end of this chapter, all tests will pass.

## 1 - Create the service file

1. Navigate to the `srv` folder in your project
2. Create a new file called `bulletinboard-service.cds`

## 2 - Import the data model

Add the following to the top of your service file to import your data model:

```cds
using com.sap.cc.bulletinboard as bb from '../db/model';
```

This imports your data model namespace and gives it the alias `bb` for easier reference.

## 3 - Define the service

Create a service that exposes the `Ads` entity:

```cds
service BulletinBoardService {
    entity Ads as projection on bb.Ads;
}
```

{: .note }
> A **projection** creates a view on top of your entity. It allows you to control which fields are exposed and how they are named in the API.

## 4 - Customize the projection with annotations

You can add annotations to control the behavior of fields in the API. Update your service definition:

```cds
service BulletinBoardService {
    entity Ads as projection on bb.Ads {
        @readonly ID        as id,
        @mandatory title,
        @mandatory contact,
        @mandatory price
    };
}
```

Let's break down the annotations:

| Annotation | Description |
|------------|-------------|
| `@readonly` | The field cannot be set by the client (e.g., auto-generated IDs) |
| `@mandatory` | The field is required when creating or updating an entity |

## 5 - Test your service

Run the application:

```bash
mvn spring-boot:run
```

Make sure that your application starts without errors. You should see an output like this in the console:
```
Started Application in 3.728 seconds (process running for 4.622)
```

{: .note }
> Open [http://localhost:8080](http://localhost:8080) in your browser to see the CAP index page. You should now see the `BulletinBoardService` with the `Ads` entity listed.

## 6 - Understanding auto-generated CRUD operations

One of the powerful features of CAP is that it automatically implements CRUD (Create, Read, Update, Delete) operations for your entities. You don't need to write any code to handle these basic operations — CAP does it for you!

Here are the default endpoints that CAP exposes for the `Ads` entity:

| HTTP Verb | CRUD | Collection (`/odata/v4/BulletinBoardService/Ads`) | Specific Item (`/odata/v4/BulletinBoardService/Ads({id})`) |
|-----------|------|---------------------------------------------------|-----------------------------------------------------------|
| GET | Read | Returns all ads | Returns a single ad |
| POST | Create | Creates a new ad | 405 Method Not Allowed |

{: .note }
> CAP also implements PUT, PATCH, and DELETE operations by default.

## 7 - Smoke testing

Open Bruno and navigate to the **Tests** folder. Right-click on the folder named **1. Defining the Service** click on **Run** and select **Recursive Run** to execute all tests in that folder. These tests correspond to the endpoints in the table above and will verify that your service is working correctly.

---

# Summary

Good job!

You have learned:
- [x] How to create a CDS service to expose your data model
- [x] How to use projections to control which fields are exposed
- [x] How to use annotations like `@readonly` and `@mandatory`
- [x] How CAP auto-generates CRUD operations for your entities
