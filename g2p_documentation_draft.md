## Summary

This API endpoint allows users to find existing genotype-phenotype associations based on a query of one or more features, phenotypes, and/or evidence terms. The API is designed to accomodate search terms (i.e. feature, phenotype, or evidence terms) specified as either a string, external identifier, ontology identifier, or as an 'entity' [See Section 391]. This flexibility in the schema allows users to specify a wide range of queries and enables a wide range of data to be stored in the database. Users will receive an array of associations as a response. Associations contain description and environment fields in addition to the relevant feature, phenotype, and evidence fields for that instance of association.

## API

### Request
The GA4GH schemas define a single endpoint `/genotypephenotype/search` which accepts a POST of a [request body](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) possibly containing a feature, phenotype, and/or evidence, which are combined as a logical AND to query the underlying datastore. Missing types are treated as a `wildcard`, returning all data.

![image](https://cloud.githubusercontent.com/assets/47808/9643362/4e081ae0-5176-11e5-8550-abd9c7c43d23.png)
http://yuml.me/edit/bf06b90a

### Response
Responses of matching data are returned as a list of  [FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132).

![image](https://cloud.githubusercontent.com/assets/47808/9339152/53d42aca-459d-11e5-8c91-204f42dc233a.png)

http://yuml.me/edit/25343da1

## Data Model
As shown in the tables below, due to the flexibility of the data model users have a number of options for how they want to specify each query term (e.g. a feature). The SearchFeaturesRequest and SearchFeaturesResponse records each have their own data structures, but share many of the same types (see the 3rd table for shared data-types). Many types rely heavily on the concept of an [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) (see end of document for discussion on usage of OntologyTerms).

The SearchFeaturesRequest record has the following data structure (super-fields listed first):

| Item  | Allowed Types/Sub-field Types | Description/Example?? |
| :------------ | :-----------: | :-------------------: |
| [SearchFeaturesRequest](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) | feature, phenotype, evidence | n/a |
| [feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L104) | `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, GenomicFeatureQuery | n/a |
| [phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L107) | `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, PhenotypeQuery | n/a |
| [evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L110) | `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, EvidenceQuery | n/a |
| [ExternalIdentifierQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L69) | ExternalIdentifier | n/a |
| [OntologyTermQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L61) | OntologyTerm | n/a |
| [GenomicFeatureQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L46) | Feature | n/a |
| [PhenotypeQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L54) | PhenotypeInstance | n/a |
| [EvidenceQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L34) | OntologyTerm | check gabriel's changes??? |

The response SearchFeaturesResponse has the following structure:

| Item  | Allowed Types/Sub-field Types | Description/Example?? |
| :------------ | :-----------: | :-------------------: |
| [SearchFeaturesResponse](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) | associations, nextPageToken | in lieu of its own line, a brief description of nextpagetoken?...not particularly important |
| [associations](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L134) | FeaturePhenotypeAssociation | n/a |
| [FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132) | id, features, evidence, phenotype, description, environmentalContexts | n/a |
| [features](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L147) | Feature | n/a |
| [evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L153) | Evidence | n/a |
| [phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L161) | PhenotypeInstance | n/a |
| [environmentalContexts](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L49) | id, OntologyTerm, description | n/a |
| [Evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L113) | OntologyTerm, description | n/a |


Shared data types:

| Item  | Allowed Types/Sub-field Types | Description/Example?? |
| :------------ | :-----------: | :-------------------: |
| [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) | ontologySource, id, name | n/a |
| [Feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | ?? | n/a |
| [PhenotypeInstance](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L77) | id, OntologyTerm, qualifier, ageOfOnset, description | n/a |
| [qualifier](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L90) | 'null', OntologyTerm | i.e. an array of OntologyTerm(s) |
| [ageOfOnset](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L97) | 'null', OntologyTerm | n/a |
| [ontologySource](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L15) | `string` | n/a |
| id | 'null', 'string' | Note: Some id's are just strings, some also take null.... |
| description | 'null', 'string' | n/a |
| name | 'null', 'string' | n/a |

## Use cases
--> I guess i should come up with this stuff

## Future work
Schema constraints: there are several fields within the schemas that are defined as non-null. This may be fine when creating an entity from a data store, however, they are problematic when creating an entity to be used in a query.

## Discussion of Usage of Ontologies
Not included here, but discussed near the end of this: https://github.com/ga4gh/g2p-team/issues/14#issuecomment-173657435
