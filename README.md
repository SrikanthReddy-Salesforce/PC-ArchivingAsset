# PC-ArchivingAsset
Enable complex rule based archving with Privacy center using just configuration changes

Solution design - Enable complex rule based archving with Privacy center using just configuration changes

Summary

Privacy center is a Salesforce product that allows creation of configuration rules to archive objects. However, it allows usage of only the object's fields in the rule, and not from a related object. We have discussed in this asset how we can overcome this limitation with a batch program that can handle multiple objects just by configruation changes, and why using a formula field does not work. This would be a good solution for projects which use Privacy center and need to work on complext archiving rules.

Details

Privacy center is a Salesforce product that allows creation of configuration rules to archive objects. However, it allows usage of only the object's fields in the rule, and not from a related object. 

Due to the way this product is architected, using formula does not work.

In this asset, we have discussed how we can overcome this limitation with a batch program that can handle multiple objects just by configruation changes. This would be a good solution for projects which use Privacy center and need to work on complext archiving rules.



Problem statement

Currently when we configure (create) policies in privacy center to archive object records based on rules. The rules are based only on the fields of the object. It does not allow to filter records based on the parent object criteria i.e we can refer fields in the policy only from the object we are trying to archive not from its parent.


Heroku postgres Database :

This is the database where the archived data and intermediate processing data is stored. To enable intermediate processing, this database has two schemas

Salesforce schema
Cache Schema
When we create a Archiving policy and run it for the first time two tables will be created for that object

One in Salesforce schema and
the other in Cache schema
The table in Cache schema contains the archived data, while the other table in Salesforce schema contains intermediate data for processing.

Whenever the policy runs, the below 2 steps will happen everytime:-

When the archiving job is run, all the records in salesforce object (for which archiving policy is created) will be copied over to the table in Salesforce schema. This will happen every time the archiving job runs, ie, it will sync the Salesforce object and the Salesforce schema table.
Once the records are in Postgres, another Heroku job will start. This job will (based on archiving policy) copy records from Salesforce schema table will be moved to Cache schema table and in the final step those records will be deleted from salesforce object and the salesforce schema table.


Final approcah :

Batch job should update the eligible records as eligible for archiving by marking a boolean field on the object to True. Privacy Center picks it up from here, and archives the records that are marked as eligible for archiving.

Other considerations:

The boolean field name should be configurable
The query to find eligible records should be configurable. This query can include conditions from the parent object.
The batch should be able to handle more than one object in a run.
Should be able to specify batch size
