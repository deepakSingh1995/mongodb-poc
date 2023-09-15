# mongodb-poc

## Using UUIDs in MongoDB

### Prerequisites

uuid package should be installed in your package.json

### Create an Opportunity with a UUID

```
const mongoose = require('mongoose');
const { v4: uuidv4 } = require('uuid');

const Opportunity = require('./models/opportunity');

const newOpportunity = new Opportunity({
  opportunityName: 'Your Opportunity Name',
  uuid: uuidv4(), // Generate a UUID for the Opportunity
  // Other Opportunity fields
});

newOpportunity.save((err) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log('Opportunity created with UUID:', newOpportunity.uuid);
});
```

### Find an Opportunity by UUID

```
const Opportunity = require('./models/opportunity');

Opportunity.findOne({ uuid: 'your-uuid-here' }, (err, opportunity) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log('Found Opportunity by UUID:', opportunity);
});

```

### Update an Opportunity by UUID

```
const Opportunity = require('./models/opportunity');

Opportunity.updateOne(
  { uuid: 'your-uuid-here' },
  { $set: { opportunityName: 'New Opportunity Name' } },
  (err, result) => {
    if (err) {
      console.error(err);
      return;
    }
    console.log('Updated Opportunity:', result);
  }
);

```

### Delete an Opportunity by UUID

```
OpportunityModel.deleteOne({ uuid: targetUUID }, (err) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log('Document with UUID deleted:', targetUUID);
});
```
## Using UUIDs vs. ObjectId in MongoDB
### Advantages of Using UUIDs

1. **Uniqueness**: UUIDs are designed to be globally unique, eliminating the risk of collisions.
```
   // Generating a Version 4 UUID in JavaScript
   const { v4: uuidv4 } = require('uuid');
   const uuid = uuidv4();
```
2. **Decentralization**: UUIDs are not tied to a centralized source or timestamp, making them suitable for distributed systems.
3. **Human-Friendly**: UUIDs can be more human-readable when represented as strings.
```
// Example UUID: "3b241101-e2bb-4255-8caf-4136c566a962"
```
4. **Compatibility** : UUIDs are widely supported across different databases and systems.

### Disadvantages of Using UUIDs
1. **Size**: UUIDs are larger than ObjectId, which can increase storage and indexing costs.
```
// Example ObjectId: "612aa6b3a23b2e001f1fc16c"
// Example UUID: "3b241101-e2bb-4255-8caf-4136c566a962"
```
2. **Indexing**: Due to their larger size, UUIDs can lead to less efficient indexing and slower queries compared to ObjectId.
```
// Example query to find by UUID
OpportunityModel.findOne({ uuid: '3b241101-e2bb-4255-8caf-4136c566a962' }, (err, opportunity) => {
  // ...
});
```
3. **Impact on Queries:**:

   a. **Populate Won't Work**

   MongoDB's `populate` method relies on ObjectId references by default. If you switch to UUIDs, you may need to manually handle population, as `populate` won't work out of the box.

      ```
   // Example: Populate with ObjectId reference (works)
   OpportunityModel.find().populate('submissions').exec((err, opportunities) => {
     // ...
   });
      ```
   b. **findById Won't Work**

   The findById method in Mongoose is optimized for ObjectId. It won't work seamlessly with UUIDs, and you'll need to use custom logic to find documents by UUID.

      ```
   // Example: findById with ObjectId (works)
    OpportunityModel.findById('612aa6b3a23b2e001f1fc16c', (err, opportunity) => {
      // ...
   });
      ```
   c. **findByIdAndUpdate Won't Work**

   Similar to findById, the findByIdAndUpdate method is optimized for ObjectId. Using it with UUIDs may require custom handling.

      ```
   // Example: findByIdAndUpdate with ObjectId (works)
    OpportunityModel.findByIdAndUpdate('612aa6b3a23b2e001f1fc16c', { status: 'Updated' }, (err, updatedOpportunity) => {
      // ...
   });
      ```

## Can we use string type instead of ObjectId inside Referenced Database for populate?
No, there is no way to do that with populate because it does index querying same as findById.

## Can we use UUID as string ?
Actually, mongodb does not any type such as UUIDs. So, UUID can be only used as string type.

