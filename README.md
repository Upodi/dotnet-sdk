# Upodi for Microsoft .NET Core Framework [![NuGet](https://img.shields.io/nuget/v/upodi.sdk.svg)](https://www.nuget.org/packages/Upodi.Sdk/)
Upodi SDK Core for C# and .net developers enables easy to use integration and programming core applications.

Upodi SDK for .NET Core 2.0.0 or .NET Framework 4.5.2 is provided by NuGet. [Download from here](https://www.nuget.org/packages/Upodi.Sdk/) or using your favorite NuGet package manager. [Source code is available on request here](https://github.com/Upodi/dotnet-sdk-source).

## Overview
* [NuGet package](https://www.nuget.org/packages/Upodi.Sdk/)
* [Documentation](https://docs.upodi.com)
* [API Documentation](https://docs.upodi.com/v2.0/reference)
* [License](https://github.com/Upodi/dotnet-sdk/blob/master/LICENSE)
* [Request a trial](https://www.upodi.com/register/walkthrough/)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/upodi)

## Getting started

### Initialization
```
UpodiService upodi = new UpodiService("{api key}");
var listOfCustomer = upodi.Customers.List();
```

### Non-Mutable Fields
Upodi SDK provides properties of entities subject to the Upodi API as documented here [https://docs.upodi.com/v2.0/reference](https://docs.upodi.com/v2.0/reference). However a few changes apply as some properties are controlled by the API - and the Upodi SDK only provides a pass through.

* **ID**. *Guid*. The ID is required to create objects via the Upodi API. However the ID will be changes upon creation as the ID is controlled by Upodi. Example: As such, you will have to create a new customer with ID = Guid.NewGuid(), which then is returned after creation with a system assigned ID.
* **CreatedDate** and **ModifiedDate**. *DateTime*. These fields are always set by Upodi.
* **CreatedBy** and **ModifiedBy**. *Guid*. These fields are always set by Upodi.

The full list of non-mutable fields can be found here [https://docs.upodi.com/v2.0/reference](https://docs.upodi.com/v2.0/reference).

### 10 line sign up
10 lines and you have signed up a customer, assigned a plan and started a subscription. This example uses Stripe.
```
// setup service
UpodiService upodi = new UpodiService("enter key here");

Guid newCustomerId = upodi.Customers.Create("70225683", "Upodi ApS", "DKK");
Guid productPlanId = Guid.Parse("- enter Guid of product plan here - "); // guid id of the product plan

upodi.SignUp.SignUp(new SignUpRequest {
      Customer = new Customer { ID = newCustomerId },
      CardToken = new CardToken { PaymentGateway = "stripe", Token = "- enter strike customer token cus_XXX -" },
      StartDate = DateTime.UtcNow,
      ProductPlan = new ProductPlan {  ID = productPlanId }
});
```

### Working with Customers
List all customers. Options to limited list all or paged results. See limited lists below.
```
var customers = upodi.Customers.List(all: true);
```

Get a customer. If not found, null is returned.
```
var customer = upodi.Customers.Get(Guid.Parse("{guid of customer}"));
if (customer != null)
  ...
```

Create a new customer. Requires accountnumber, fullname and currencycode.
```
var newId = upodi.Customers.Create("acocuntnumber", "fullname", "USD");
```

### Working with Subscriptions
List all subscriptions. Options to limited list all or paged results. See limited lists below.
```
var subscriptions = upodi.Subscriptions.List(all: true);
```

Create a new subscription. Requires the customer and product plan id.
```
var newId = upodi.Subscriptions.Create(customerId, productPlanId, DateTime.UtcNow);
```

Subscriptions enable various actions.
```
var result = upodi.Subscriptions.Activate(subscriptionId);
var result = upodi.Subscriptions.Cancel(subscriptionId);
var result = upodi.Subscriptions.Expire(subscriptionId);
var result = upodi.Subscriptions.Hold(subscriptionId);
var result = upodi.Subscriptions.Resume(subscriptionId);
```

List active charges (parameter true).
```
var charges = upodi.Subscriptions.ListCharges(subscriptionId, true);
```

Update amount of a subscription charge.
```
var result = upodi.Subscriptions.SetAmount(subscriptionId, productPlanChargeId, 302.34);
```
## Limited lists
Using the .List() method on customer and subscription (ex. upodi.Subscriptions.List()) will return a reduced number of records due to performance. You will have to iterate through the list fetching each object to get the full object.

| List          | Properties    |
| ------------- | ------------- |
| Customer      | ID,CompanyName,AccountNumber,CurrencyCode,FullName,AutoBill,RefKey,CreatedDate,ModifiedDate |
| Subscription  | ID,SubscriptionNumber,CustomerID,ProductPlanID,StartDate,StateCode,Status,EndDate,RefKey,CreatedDate,ModifiedDate      |
