---
title: Defining the Data Model
layout: home
nav_order: 3
---

# Objectives
- Learn how to define entities using CDS (Core Data Services)
- Understand how to use built-in CAP types and aspects
- Define custom types for reusable structures

# Wait, but why?
In CAP, the data model is the foundation of your application. It defines the structure of your data and the relationships between entities. Using CDS (Core Data Services), you can define your data model in a declarative way that is database-agnostic. CAP will automatically generate the database schema for you.

---

# Exercise

## 1 - Create the data model file

1. Navigate to the `db` folder in your project
2. Create a new file called `model.cds`

## 2 - Define the namespace and imports

Add the following to the top of your `model.cds` file:

```cds
namespace com.sap.cc.bulletinboard;

using {
    cuid,
    Currency
} from '@sap/cds/common';
```

{: .note }
> **What are these imports?**
> - `cuid` - Provides a unique identifier field (`ID`) using UUIDs
> - `Currency` - A built-in type for handling currencies

## 3 - Define a custom type

Add the `Price` type to encapsulate price-related fields:

```cds
type Price {
    value    : Decimal(10, 3);
    currency : Currency
}
```

{: .note }
> Custom types help you group related fields together and reuse them across multiple entities.

## 4 - Define the Ads entity

Add the `Ads` entity to your model:

```cds
entity Ads : cuid {
    title  : String;
    price  : Price;
    active : Boolean default true;
}
```

Let's break this down:

| Element | Description |
|---------|-------------|
| `entity Ads : cuid` | Creates an entity that inherits a UUID-based `ID` field |
| `title : String` | A simple string field for the ad title |
| `price : Price` | Uses the custom `Price` type defined above |
| `active : Boolean default true` | A boolean with a default value |

## 5 - Verify the complete model

Your complete `db/model.cds` should look like this:

```cds
namespace com.sap.cc.bulletinboard;

using {
    cuid,
    Currency
} from '@sap/cds/common';

type Price {
    value    : Decimal(10, 3);
    currency : Currency
}

entity Ads : cuid {
    title  : String;
    price  : Price;
    active : Boolean default true;
}
```

## 6 - Test your data model

Run the application to verify your data model compiles correctly:

```bash
mvn spring-boot:run
```

If there are no errors, CAP has successfully parsed your data model and generated the corresponding database schema.

---

# Summary

Good job!

You have learned:
- [x] How to define a namespace and import common CAP types
- [x] How to create entities with various field types
- [x] How to use built-in aspects like `cuid` for UUID primary keys
- [x] How to define custom types for reusable structures
