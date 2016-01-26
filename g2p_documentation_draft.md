# _DRAFT_

## Summary
This endpoint allows users to query an `AND` of `(feature && phenotype && evidence)` returning a set of Associations.  Each of these query terms can be expressed as  either a  [string, external identifier, ontology identifier and 'entity'].  The associations returned are an array of `[ features[],evidence[],phenotype,description, environment[] ]`, each of these concepts have the same flexible representation as the query terms.

## API
The GA4GH schemas define a single endpoint `/genotypephenotype/search` which accepts a POST of a [request body](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102)  containing one or more of [Feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105), [PhenotypeInstance](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L77), [EnvironmentalContext](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L49), and [Evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L113) which are combined as a logical AND to query the underlying datastore.  Missing types are treated as a `wildcard` returning all data.  Responses of matching data are returned as a list of  [FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132).  All types rely heavily on [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10)

### Request
![image](https://cloud.githubusercontent.com/assets/47808/9643362/4e081ae0-5176-11e5-8550-abd9c7c43d23.png)
http://yuml.me/edit/bf06b90a

### Response
![image](https://cloud.githubusercontent.com/assets/47808/9339152/53d42aca-459d-11e5-8c91-204f42dc233a.png)

http://yuml.me/edit/25343da1

### Data Model

Each concept ([Feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105), [PhenotypeInstance](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L77), [EnvironmentalContext](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L49), and [Evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L113)) can potentially be represented in increasing specificity as either a  [string, external identifier, ontology identifier and 'entity'].


https://github.com/ga4gh/schemas/issues/165

Following discussion on the MTT here is a proposal refining the definition and representation of ontologies and annotations in ontology.avdl.

Intent:
The GA4GH Ontology schema provides structures for unambiguous references to ontological concepts and/or controlled vocabularies within AVRO. The structures provided are not intended for de novo modeling of ontologies, or representing complete ontologies within AVRO. References to e.g. classes from external ontologies or controlled vocabularies should be interpreted only in their original context i.e. the source ontology.

Usage
Multiple ontology terms can be supplied e.g. to describe a series of phenotypes for a specific sample. The ontology.avdl is not intended to model relationships between terms, or to provide mappings between ontologies for the same concept. Should an OntologyTerm be unavailable, or terms unmapped then an 'annotation' can be provided which can later be mapped to an ontology term using a service designed for this. Using OntologyTerm is preferred to using Annotation. Though annotations can be supplied with related ontology terms if desired. A use case could be when a free text annotation is very specific and a more general OntologyTerm is supplied.

New:
Annotation - A free text annotation which is not an ontology term describing some attribute. Annotations have associations with OntologyTerms to allow these to be added after annotations are captured. OntologyTerms are preferred over Annotations in all cases. Annotations can be used in conjucntion with OntologyTerms

Newly defined
OntologyTerm - the preferred term for the class in question. For example http://purl.obolibrary.org/obo/HP_0011927 preferred term is 'short digit' and synonym is 'VERY SHORT DIGIT'. 'short digit' is the term that should be used.

Newly defined
OntologyTerm identifier - An identifier for a single ontology term from a single ontology source specified as a CURIE (preferred) or PURL

Newly defined
OntologySource - the name of ontology from which the term is obtained. e.g. 'Human Phenotype Ontology'

Newly Defined
OntologySource identifier - the identifier -a CURIE (preferred) or PURL for an ontology source e.g. http://purl.obolibrary.org/obo/hp.obo

Newly defined
OntologySource version - the version of the ontology from which the OntologyTerm is obtained. E.g. 2.6.1. There is no standard for ontology versioning and some frequently released ontologies may use a datestamp, or build number.


```
  /**
  The ID defined by the external onotology source.
  (e.g. `http://purl.obolibrary.org/obo/OBI_0001271`)
  */
  string id;
```

This is fairly open ended and we can imagine confusion and inconsistent usage here.

For the ontologies currently referenced in the metadata schema, e.g.

 * HP
 * UBERON
 * CL
 * OBI

Terms are typically referenced in two ways.

## URIs/IRIs

For many biological ontologies these are typically obolibrary purls, which follow:

    http://purl.obolibrary.org/obo/<IDSPACE>_<NUMERICFRAGMENT>

See: http://www.obofoundry.org/id-policy.shtml

## OBO-Style identifiers

Typically follow the form

    <IDSPACE>:<NUMERICFRAGMENT>

## Implementation Options

1. The schema should mandate URIs only (using the URI form recommended by the source ontology)
2. The schema should mandate OBO-Style IDs
3. The schema should have separate 'id' and 'iri' fields
4. The schema should have a flexible field

The current majority opinion is to have "polymorphic" ways to reference ontologies, and also to include a way to reference/use local ontologies (e.g. if no best fit reference is known/found, or for legacy => convert later etc.). Also, for records like "disease" (which has yet to be defined as record type), we found the most sensible way to allow references to multiple ontologies, with one being the primary (doodling here):

Ideally we can stick to CURIEs and standardize prefixs as we see lots of messes where this has not been done.

Some issues we need to think about are:
a) how to reference an ontology class or property in the avro schemas, taking into account the provenance
b) how to specify the domain or range in any given context, and allowable/useful sources - e.g. as per above comments, we don't want to limit people in their choices of ontologies as there are different needs, but at the same time, we want to limit the misuse of incorrect semantic types (e.g. not equate a variant with a gene, or a phenotype with a disease)
c) how to represent IDs. You can see an example from Monarch where we have a CURIE map in a YAML file:
https://github.com/monarch-initiative/dipper/blob/afd2d11bd6356f37333c5514c8d38071e02f1e58/dipper/curie_map.yaml

https://github.com/ga4gh/schemas/issues/350
Following discussion on the MTT here is a proposal refining the definition and representation of ontologies and annotations in ontology.avdl.

Intent:
The GA4GH Ontology schema provides structures for unambiguous references to ontological concepts and/or controlled vocabularies within AVRO. The structures provided are not intended for de novo modeling of ontologies, or representing complete ontologies within AVRO. References to e.g. classes from external ontologies or controlled vocabularies should be interpreted only in their original context i.e. the source ontology.

Usage
Multiple ontology terms can be supplied e.g. to describe a series of phenotypes for a specific sample. The ontology.avdl is not intended to model relationships between terms, or to provide mappings between ontologies for the same concept. Should an OntologyTerm be unavailable, or terms unmapped then an 'annotation' can be provided which can later be mapped to an ontology term using a service designed for this. Using OntologyTerm is preferred to using Annotation. Though annotations can be supplied with related ontology terms if desired. A use case could be when a free text annotation is very specific and a more general OntologyTerm is supplied.

New:
Annotation - A free text annotation which is not an ontology term describing some attribute. Annotations have associations with OntologyTerms to allow these to be added after annotations are captured. OntologyTerms are preferred over Annotations in all cases. Annotations can be used in conjucntion with OntologyTerms

Newly defined
OntologyTerm - the preferred term for the class in question. For example http://purl.obolibrary.org/obo/HP_0011927 preferred term is 'short digit' and synonym is 'VERY SHORT DIGIT'. 'short digit' is the term that should be used.

Newly defined
OntologyTerm identifier - An identifier for a single ontology term from a single ontology source specified as a CURIE (preferred) or PURL

Newly defined
OntologySource - the name of ontology from which the term is obtained. e.g. 'Human Phenotype Ontology'

Newly Defined
OntologySource identifier - the identifier -a CURIE (preferred) or PURL for an ontology source e.g. http://purl.obolibrary.org/obo/hp.obo

Newly defined
OntologySource version - the version of the ontology from which the OntologyTerm is obtained. E.g. 2.6.1. There is no standard for ontology versioning and some frequently released ontologies may use a datestamp, or build number.


## Use cases
`//TODO`

## Future work
Schema constraints: there are several fields within the schemas that are defined as non-null. This may be fine when creating an entity from a data store, however, they are problematic when creating an entity to be used in a query.
