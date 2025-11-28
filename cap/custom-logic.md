---
title: Adding Custom Logic
layout: home
nav_order: 5
---

# Objectives
- Learn how to add custom actions to your service
- Understand how to implement action handlers in Java
- Learn how to use the Persistence Service to update entities

# Wait, but why?
While CAP automatically provides CRUD operations, real-world applications often need custom business logic. CAP allows you to define custom actions and implement them in Java. This gives you the flexibility to add any business logic you need while still benefiting from CAP's built-in features.

---

# Exercise

## 0 - Run the tests first

Before starting, open Bruno and navigate to the **Tests** folder. Right-click on the folder named **2. Adding Custom Logic**, click on **Run** and select **Recursive Run**. You should see all tests fail — this is expected! By the end of this chapter, all tests will pass.

## 1 - Add the active field to the data model

First, we need to add an `active` field to our `Ads` entity. Open `db/model.cds` and update the entity:

```cds
entity Ads : cuid {
    title   : String;
    contact : String;
    price   : Price;
    active  : Boolean default true;
}
```

The `active` field will be used to mark ads as active or inactive.

## 2 - Expose the active field in the service

Update your `srv/bulletinboard-service.cds` to expose the `active` field as readonly:

```cds
service BulletinBoardService {
    entity Ads as projection on bb.Ads {
        @readonly ID        as id,
        @mandatory title,
        @mandatory contact,
        @mandatory price,
        @readonly active
    };
}
```

{: .note }
> The `active` field is marked as `@readonly` because we don't want clients to set it directly. Instead, they will use a custom action.

## 3 - Define the setActive action

Add a custom action to the `Ads` entity in your service definition:

```cds
service BulletinBoardService {
    entity Ads as projection on bb.Ads {
        @readonly ID        as id,
        @mandatory title,
        @mandatory contact,
        @mandatory price,
        @readonly active
    } actions {
        action setActive(@mandatory isActive : Boolean) returns Ads;
    };
}
```

Let's break down the action definition:

| Element | Description |
|---------|-------------|
| `action setActive` | Defines a bound action named `setActive` on the `Ads` entity |
| `@mandatory isActive : Boolean` | A required parameter of type Boolean |
| `returns Ads` | The action returns the updated `Ads` entity |

{: .note }
> A **bound action** is an action that is called on a specific entity instance, e.g., `/Ads({id})/setActive`.

## 4 - Create the Java handler class

Now we need to implement the action in Java. Create a new file at `srv/src/main/java/customer/bulletinboard/services/BulletinBoardServiceHandler.java`:

```java
package customer.bulletinboard.services;

import cds.gen.bulletinboardservice.*;
import com.sap.cds.Result;
import com.sap.cds.ql.Select;
import com.sap.cds.ql.Update;
import com.sap.cds.ql.cqn.AnalysisResult;
import com.sap.cds.ql.cqn.CqnAnalyzer;
import com.sap.cds.ql.cqn.CqnSelect;
import com.sap.cds.ql.cqn.CqnUpdate;
import com.sap.cds.services.ErrorStatuses;
import com.sap.cds.services.ServiceException;
import com.sap.cds.services.handler.EventHandler;
import com.sap.cds.services.handler.annotations.On;
import com.sap.cds.services.handler.annotations.ServiceName;
import com.sap.cds.services.persistence.PersistenceService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.Optional;

@Component
@ServiceName(BulletinBoardService_.CDS_NAME)
public class BulletinBoardServiceHandler implements EventHandler {

    @Autowired
    private PersistenceService persistenceService;

}
```

Let's break down the key annotations:

| Annotation | Description |
|------------|-------------|
| `@Component` | Marks this class as a Spring component |
| `@ServiceName` | Binds this handler to the `BulletinBoardService` |
| `@Autowired` | Injects the `PersistenceService` for database operations |

## 5 - Implement the setActive action

Add the following method to your handler class:

```java
@On(event = "setActive")
public void setActive(AdsSetActiveContext context) {
    // 1. Extract the entity ID from the request
    CqnAnalyzer analyzer = CqnAnalyzer.create(context.getModel());
    AnalysisResult analysisResult = analyzer.analyze(context.getCqn());
    var rootKeys = analysisResult.rootKeys();

    // 2. Fetch the ad from the database
    CqnSelect adSelect = Select.from(context.getTarget()).byId(rootKeys.get(Ads.ID));
    Result selectResult = persistenceService.run(adSelect);

    Optional<Ads> adOptional = selectResult.first(Ads.class);
    if (adOptional.isEmpty()) {
        throw new ServiceException(ErrorStatuses.NOT_FOUND, "Invalid ad ID!");
    }

    // 3. Update the active field
    Ads ad = adOptional.get();
    ad.setActive(context.getIsActive());

    // 4. Persist the changes
    CqnUpdate update = Update.entity(Ads_.class).entry(ad);
    persistenceService.run(update);

    // 5. Return the updated entity
    context.setResult(ad);
}
```

Let's break down the implementation:

| Step | Description |
|------|-------------|
| 1 | Use `CqnAnalyzer` to extract the entity ID from the incoming request |
| 2 | Use `Select` to fetch the ad from the database |
| 3 | Update the `active` field with the value from the request parameter |
| 4 | Use `Update` to persist the changes to the database |
| 5 | Return the updated entity to the client |

{: .note }
> The `AdsSetActiveContext` class is auto-generated by CAP based on your action definition.

## 6 - Test your implementation

Run the application:

```bash
mvn spring-boot:run
```

## 7 - Smoke testing

Open Bruno and navigate to the **Tests** folder. Right-click on the folder named **2. Adding Custom Logic**, click on **Run** and select **Recursive Run** to execute all tests. All tests should now pass.

---

# Summary

Good job!

You have learned:
- [x] How to define custom actions on entities
- [x] How to create a Java handler class for your service
- [x] How to use the `@On` annotation to handle custom actions
- [x] How to use the Persistence Service to read and update entities
- [x] How to return results from custom actions
