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

## 5 - Verify the complete service

Your complete `srv/bulletinboard-service.cds` should look like this:

```cds
using com.sap.cc.bulletinboard as bb from '../db/model';

service BulletinBoardService {
    entity Ads as projection on bb.Ads {
        @readonly ID        as id,
        @mandatory title,
        @mandatory contact,
        @mandatory price
    };
}
```

## 6 - Test your service

Run the application:

```bash
mvn spring-boot:run
```

{: .note }
> Open [http://localhost:8080](http://localhost:8080) in your browser to see the CAP index page. You should now see the `BulletinBoardService` with the `Ads` entity listed.

## 7 - Understanding auto-generated CRUD operations

One of the powerful features of CAP is that it automatically implements CRUD (Create, Read, Update, Delete) operations for your entities. You don't need to write any code to handle these basic operations — CAP does it for you!

Here are the default endpoints that CAP exposes for the `Ads` entity:

| HTTP Verb | CRUD | Collection (`/odata/v4/BulletinBoardService/Ads`) | Specific Item (`/odata/v4/BulletinBoardService/Ads({id})`) |
|-----------|------|---------------------------------------------------|-----------------------------------------------------------|
| GET | Read | Returns all ads | Returns a single ad |
| POST | Create | Creates a new ad | 405 Method Not Allowed |

{: .note }
> CAP also implements PUT, PATCH, and DELETE operations by default. You can test these endpoints using Bruno or any other HTTP client.

## 8 - Smoke testing

Use Bruno (or any HTTP client) to test the auto-generated endpoints:

1. **Create a new ad** - Send a POST request to `/odata/v4/BulletinBoardService/Ads`:

   ```json
   {
     "title": "Selling my old bike",
     "contact": "bike@example.com",
     "price_value": 150.00,
     "price_currency_code": "EUR"
   }
   ```

2. **Retrieve all ads** - Send a GET request to `/odata/v4/BulletinBoardService/Ads`

3. **Retrieve a single ad** - Send a GET request to `/odata/v4/BulletinBoardService/Ads({id})`, replacing `{id}` with the UUID from the created ad

Verify that:
- [ ] POST to the collection returns `201 Created` with the new ad
- [ ] GET to the collection returns a list containing your ad
- [ ] GET to a specific item returns the ad details

---

# Summary

Good job!

You have learned:
- [x] How to create a CDS service to expose your data model
- [x] How to use projections to control which fields are exposed
- [x] How to use annotations like `@readonly` and `@mandatory`
- [x] How CAP auto-generates CRUD operations for your entities
- [x] How to test your service endpoints
