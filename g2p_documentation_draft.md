## Summary

This API endpoint allows users to search for genotype-phenotype associations in a GA4GH datastore. The user can search for associations by building queries composed of features, phenotypes, and/or evidence terms. The API is designed to accommodate search terms specified as either a string, external identifier, ontology identifier, or as an 'entity' (See Data Model section). These terms are combined as an `AND` of `(feature && phenotype && evidence)`. This flexibility in the schema allows a variety of data to be stored in the database and allows users to express a wide range of queries.

Users will receive an array of associations as a response. Associations contain description and environment fields in addition to the relevant feature, phenotype, and evidence fields for that instance of association.


## API

### Request
The G2P schemas define a single endpoint `/genotypephenotype/search` which accepts a POST of a [request body](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) in [JSON](http://json.org/example.html) format. The request may contain a feature, phenotype, and/or evidence, which are combined as a logical AND to query the underlying datastore. Missing types are treated as a wildcard, returning all data.

### Response
[Responses](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) of matching data are returned as a list of  [`FeaturePhenotypeAssociation`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132)s.

## Data Model

Intent:
The GA4GH Ontology schema provides structures for unambiguous references to ontological concepts and/or controlled vocabularies within AVRO. The structures provided are not intended for de novo modeling of ontologies, or representing complete ontologies within AVRO. References to e.g. classes from external ontologies or controlled vocabularies should be interpreted only in their original context i.e. the source ontology.
 
Due to the flexibility of the data model, users have a number of options for specifying each query term ([feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L105), [phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L108), and [evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L111)). For instance, a feature can potentially be represented in increasing specificity as either a [string, an ontology identifier, an external identifier, or as a feature 'entity'].

The POST data sent as part of `/genotypephenotype/search` must be in JSON format and must obey the [`SearchFeaturesRequest`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) schema. [`SearchFeaturesResponse`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) is the response from `POST /genotypephenotype/search`, also expressed as JSON.

As shown below, the `SearchFeaturesRequest` and `SearchFeaturesResponse` records each have their own data structures, but they use many of the same types (see the 3rd table for shared data-types). Many types rely heavily on the concept of an [`OntologyTerm`](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) (see end of document for discussion on usage of `OntologyTerm`s).

The `SearchFeaturesRequest` record has the following data structure (sub-fields are indented):  
<sub>Note: Externally shared data types like `ExternalIdentifier`, `OntologyTerm`, `Feature`, and `PhenotypeInstance` are defined in the 3rd table. Shared data types specific to `SearchFeaturesRequest` are defined within the table in a **Shared Data Types** section.<sub/>

| Item  | Allowed Types/Sub-field Types |
| :------------ | :-----------: |
| [SearchFeaturesRequest](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) | feature, phenotype, evidence |
| &nbsp;&nbsp;&nbsp;-&nbsp;[feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L105) | *union*: `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, GenomicFeatureQuery |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[GenomicFeatureQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L46) | Feature |
| &nbsp;&nbsp;&nbsp;-&nbsp;[phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L108) | *union*: `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, PhenotypeQuery |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[PhenotypeQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L54) | PhenotypeInstance |
| &nbsp;&nbsp;&nbsp;-&nbsp;[evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L111) | *union*: `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, EvidenceQuery |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[EvidenceQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L34) | OntologyTerm |
| **Shared Data Types** ||
| [ExternalIdentifierQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L69) | ExternalIdentifier |
| [OntologyTermQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L61) | OntologyTerm |

`SearchFeaturesResponse` has the following structure:

| Item  | Allowed Types/Sub-field Types |
| :------------ | :-----------: |
| [SearchFeaturesResponse](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) | associations, [nextPageToken](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L141) |
| &nbsp;&nbsp;&nbsp;-&nbsp;[associations](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L134) | *array*: FeaturePhenotypeAssociation |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132) | id, features, evidence, phenotype, description, environmentalContexts |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[features](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L147) | Feature |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L153) | [Evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L113): evidenceType, description |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[evidenceType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L116) | OntologyTerm |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L161) | PhenotypeInstance |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[environmentalContexts](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L49) | id, environmentType, description |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-&nbsp;[environmentType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L61) | OntologyTerm |


Data types used by both `SearchFeaturesRequest` and `SearchFeaturesResponse`:

| Item  | Allowed Types/Sub-field Types |
| :------------ | :-----------: |
| [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) | ontologySource, id, name |
| &nbsp;&nbsp;&nbsp;-&nbsp;[ontologySource](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L15) | `string` |
| [Feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | id, parentIds, featureSetId, referenceName, start, end, strand, featureType, attributes <sub>Note: The first six fields here are strings or arrays of strings<sub/> |
| &nbsp;&nbsp;&nbsp;-&nbsp;[strand](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L134) | *union*: `null`, [Strand](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/common.avdl#L13) |
| &nbsp;&nbsp;&nbsp;-&nbsp;[featureType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | OntologyTerm |
| &nbsp;&nbsp;&nbsp;-&nbsp;[attributes](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L54) | *array* vals: `string`, ExternalIdentifier, OntologyTerm |
| [PhenotypeInstance](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L77) | id, OntologyTerm, qualifier, ageOfOnset, description |
| &nbsp;&nbsp;&nbsp;-&nbsp;[qualifier](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L90) | *array*: `null`, OntologyTerm |
| &nbsp;&nbsp;&nbsp;-&nbsp;[ageOfOnset](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L97) | *union*: `null`, OntologyTerm |
| id | `null`, `string` <sub>Note: Not all `id`s allow `null`<sub/> |
| description | `null`, `string` |
| name | `null`, `string` |

## Use cases

1) As a clinician or a genomics researcher, I may have a patient with Gastrointestinal stromal tumor, GIST, and a proposed drug for treatment, imatinib. In order to identify whether the patient would respond well to treatment with the drug, I need a list of features (e.g. genes) which are associated with the sensitivity of GIST to imatinib. Suppose I am specifically interested in a gene, *KIT*, which is implicated in the pathogenesis of several cancer types. I could submit a query to `/genotypephenotype/search` with GIST as the phenotype, *KIT* as the feature, and [`clinical study evidence`](http://purl.obolibrary.org/obo/ECO_0000180) as the evidence.

In response, I will receive back a list of associations involving GIST and *KIT*, which I can filter for instances where imatinib is mentioned. URI's in the `associations` field could - hypothetically - be followed to discover that [GIST patients with wild-type *KIT* have decreased sensitivity to therapy with imatinib](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC2651076/).

If I left both the `genotype` and `evidence` fields as `null`, I would receive back all associations which involve GIST as a phenotype.

2) As a non-Hodgkin's lymphoma researcher, I may know that the gene *CD20* has abnormal expression in [`Hodgkin's lymphoma`](http://purl.obolibrary.org/obo/DOID_8567). I might be interested in knowing whether *CD20* also has abnormal expression in [`non-Hodgkin lymphoma`](http://purl.obolibrary.org/obo/DOID_0060060). Therefore I could perform a query with *CD20* as a feature, non-Hodgkin's lymphoma as a phenotype, and [`RNA sequencing`](http://purl.obolibrary.org/obo/OBI_0001177) as the evidence type.

3) As a genetic counselor, I may be wondering if a mutation in one of my clients' genes has ever been associated with a disease. I could then do a query based on the gene name as the feature and [`disease`](http://purl.obolibrary.org/obo/DOID_4) as the phenotype.


## Ontologies

Usage:
Multiple ontology terms can be supplied e.g. to describe a series of phenotypes for a specific sample. The ontology.avdl is not intended to model relationships between terms, or to provide mappings between ontologies for the same concept. Should an OntologyTerm be unavailable, or terms unmapped then an 'annotation' can be provided which can later be mapped to an ontology term using a service designed for this. Using OntologyTerm is preferred to using Annotation. Though annotations can be supplied with related ontology terms if desired. A use case could be when a free text annotation is very specific and a more general OntologyTerm is supplied.

Definitions: 
Annotation - A free text annotation which is not an ontology term describing some attribute. Annotations have associations with OntologyTerms to allow these to be added after annotations are captured. OntologyTerms are preferred over Annotations in all cases. Annotations can be used in conjucntion with OntologyTerms

OntologyTerm - the preferred term for the class in question. For example http://purl.obolibrary.org/obo/HP_0011927 preferred term is 'short digit' and synonym is 'VERY SHORT DIGIT'. 'short digit' is the term that should be used.

OntologyTerm identifier - An identifier for a single ontology term from a single ontology source specified as a CURIE (preferred) or PURL

OntologySource - the name of ontology from which the term is obtained. e.g. 'Human Phenotype Ontology'

OntologySource identifier - the identifier -a CURIE (preferred) or PURL for an ontology source e.g. http://purl.obolibrary.org/obo/hp.obo

OntologySource version - the version of the ontology from which the OntologyTerm is obtained. E.g. 2.6.1. There is no standard for ontology versioning and some frequently released ontologies may use a datestamp, or build number.
