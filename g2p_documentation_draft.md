## Summary

This API endpoint allows users to search for genotype-phenotype associations in a GA4GH datastore. The user can search for associations by building queries composed of features, phenotypes, and/or evidence terms. The API is designed to accommodate search terms specified as either a string, external identifier, ontology identifier, or as an 'entity' (See Data Model section). These terms are combined as an `AND` of `(feature && phenotype && evidence)`. This flexibility in the schema allows a variety of data to be stored in the database and allows users to express a wide range of queries.

Users will receive an array of associations as a response. Associations contain description and environment fields in addition to the relevant feature, phenotype, and evidence fields for that instance of association.


## API

### Request
The G2P schemas define a single endpoint `/genotypephenotype/search` which accepts a POST of a [request body](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) in [JSON](http://json.org/example.html) format. The request may contain a feature, phenotype, and/or evidence, which are combined as a logical AND to query the underlying datastore. Missing types are treated as a wildcard, returning all data.

### Response
[Responses](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) of matching data are returned as a list of  [`FeaturePhenotypeAssociation`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132)s.

## Data Model
The POST data sent as part of `/genotypephenotype/search` must be in JSON format and must obey the [`SearchFeaturesRequest`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) schema. [`SearchFeaturesResponse`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) is the response from `POST /genotypephenotype/search`, also expressed as JSON.

As shown below, the `SearchFeaturesRequest` and `SearchFeaturesResponse` records each have their own data structures, but they use many of the same types (see the 3rd table for shared data-types). Many types rely heavily on the concept of an [`OntologyTerm`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) (see end of document for discussion on usage of `OntologyTerm`s).

Due to the flexibility of the data model, users have a number of options for specifying each query term. For instance, a feature can be defined as either a string, an external identifier, an ontology identifier, or as a feature 'entity'.

The `SearchFeaturesRequest` record has the following data structure (sub-fields are indented):  
<sub>Note: Externally shared data types like `ExternalIdentifier`, `OntologyTerm`, `Feature`, and `PhenotypeInstance` are defined in the 3rd table. Shared data types specific to `SearchFeaturesRequest` are defined within the table in a **Shared Data Types** section.<sub/>

| Item  | Allowed Types/Sub-field Types |
| :------------ | :-----------: |
| [SearchFeaturesRequest](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) | feature, phenotype, evidence |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L105) | *union*: `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, GenomicFeatureQuery |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[GenomicFeatureQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L46) | Feature |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L108) | *union*: `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, PhenotypeQuery |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[PhenotypeQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L54) | PhenotypeInstance |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L111) | *union*: `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, EvidenceQuery |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[EvidenceQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L34) | OntologyTerm |
| **Shared Data Types** ||
| [ExternalIdentifierQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L69) | ExternalIdentifier |
| [OntologyTermQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L61) | OntologyTerm |

`SearchFeaturesResponse` has the following structure:

| Item  | Allowed Types/Sub-field Types |
| :------------ | :-----------: |
| [SearchFeaturesResponse](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) | associations, [nextPageToken](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L141) |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[associations](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L134) | *array*: FeaturePhenotypeAssociation |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132) | id, features, evidence, phenotype, description, environmentalContexts |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[features](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L147) | Feature |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L153) | [Evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L113): evidenceType, description |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[evidenceType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L116) | OntologyTerm |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L161) | PhenotypeInstance |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[environmentalContexts](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L49) | id, environmentType, description |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[environmentType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L61) | OntologyTerm |


Data types used by both `SearchFeaturesRequest` and `SearchFeaturesResponse`:

| Item  | Allowed Types/Sub-field Types |
| :------------ | :-----------: |
| [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) | ontologySource, id, name |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[ontologySource](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L15) | `string` |
| [Feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | id, parentIds, featureSetId, referenceName, start, end, strand, featureType, attributes <sub>Note: The first six fields here are strings or arrays of strings<sub/> |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[strand](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L134) | *union*: `null`, [Strand](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/common.avdl#L13) |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[featureType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | OntologyTerm |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[attributes](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L54) | *array* vals: `string`, ExternalIdentifier, OntologyTerm |
| [PhenotypeInstance](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L77) | id, OntologyTerm, qualifier, ageOfOnset, description |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[qualifier](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L90) | *array*: `null`, OntologyTerm |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[ageOfOnset](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L97) | *union*: `null`, OntologyTerm |
| id | `null`, `string` <sub>Note: Not all `id`s allow `null`<sub/> |
| description | `null`, `string` |
| name | `null`, `string` |

## Use cases



1) This is an example of a query formatted in JSON. (Ideally I should explain the biological significance of the query here, say `37da8697` has to do with a phenotype like "back pain") In this case, the `phenotype`, `feature`, and `evidence` fields are all defined using `ExternalIdentifier`s which point to a specific item in a database. 
<pre>
{
  "pageToken": null,
  "phenotype": {
    "ids": [
      {
        "identifier": "37da8697",
        "version": "*",
        "database": "http:\/\/ohsu.edu\/cgd\/"
      }
    ]
  },
  "feature": {
    "ids": [
      {
        "identifier": "4841bf74",
        "version": "*",
        "database": "http:\/\/ohsu.edu\/cgd\/"
      }
    ]
  },
  "pageSize": null,
  "evidence": {
    "ids": [
      {
        "identifier": "DB00619",
        "version": "*",
        "database": "FOO"
      }
    ]
  }
}
</pre>

2) This query defines `feature` but leaves the `phenotype` and `evidence` fields undefined. All associations which include the specified `feature` (which is defined using the `ExternalIdentifier` type) will be returned.
<pre>
{
  "pageToken": null,
  "phenotype": null,
  "feature": {
    "ids": [
      {
        "identifier": "4841bf74",
        "version": "*",
        "database": "http:\/\/ohsu.edu\/cgd\/"
      }
    ]
  },
  "pageSize": null,
  "evidence": null
}
</pre>

3) 

As a [role], given [some pre-condition], when [some event], in order to [get some value], I [want the system to do this]

As a genomics researcher,  given a patient with a cold, when I have a proposed drug, in order to identify what features have the best response, I need a list of features



This query defines the `phenotype`, `feature`, and `evidence` terms using strings. When this occurs, the database is searched for all matches to those strings (not sure about the specifics here, or how the *wild syntax works). `GIST` stands for 'Gastrointestinal stromal tumor', and `imatinib` is a cancer drug. `KIT` is a gene implicated in the pathogenesis of several cancer types, including GIST.
<pre>
{
  "pageToken": null,
  "phenotype": "GIST",
  "feature": "KIT *wild",
  "pageSize": null,
  "evidence": "imatinib"
}
</pre>

4) It might be good to include a query which uses `GenomicFeatureQuery` or an `OntologyTermQuery` which is an array of multiple `OntologyTerms`s. This will demonstrate more use cases....

## Future work
(copied from old document)
Schema constraints: there are several fields within the schemas that are defined as non-null. This may be fine when creating an entity from a data store, however, they are problematic when creating an entity to be used in a query.

## Discussion of Ontologies

** BW: I can handle this...  
I'm not sure how we want to include this. See the bottom of this page: https://github.com/ga4gh/g2p-team/issues/14#issuecomment-173657435
