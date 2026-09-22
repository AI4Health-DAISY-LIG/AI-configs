# Biolink Model Documentation 


This document gives an overview of the BioLink model, its structure, and the way it functions.

## Introduction: knowledge graphs KG

Knowledge graphs are a way of representing human knowledge in a computable form. They use a semantic network, in which nodes represent entities and edges represent links/relationships between entities.

Knowledge graphs (KGs) make it easy to decompose knowledge in simple facts, it allow deductive inference through logical rules. They can also serve as a store of information for various application or even be embedded into vector spaces that allows for their use in neural networks.

Each KG is developed for a specific task, with a specific vocabulary, in a specific form. It typically lack schemas 

The main goal is to standardize knowledge graphes and to make there interpretable in order to pool all the KGs together. 
The Biolink model was developed as a part of the standardization efforts in the NCATS Biomedical Data Translator Project ([Link](https://ncatstranslator.github.io/TranslatorTechnicalDocumentation/))

Some open source knowledge examples: Wikidata and dbpedia  built using semantic web technologies.
Some examples of integrated KGs IN LIFE SCIENCE / SEMANTIC midline DATABASE, HETIONET,WikiDatata, Monarch Initiative, Bio2RDF..

## The Biolink Model

Biolink Model is a high-level data model for representing biological and biomedical knowledge. It can be used to formalize the relationships between data structures in translational science. It incorporates object-oriented classification and graph-oriented features. The core of the model is a set of hierarchical, interconnected classes (or categories) and relationships between them (or predicates).

The model consists of nodes(entities), edges(associations), predicates and properties; entities that are arranged in a hierarchy and represents entities found in biological and biomedical knowledge (genes, proteins,phenotopic feature..). Each entity has its specific parameters: its own stable URI, mappings to other ontologies and a list of valid ID prefixes.

The edges are association types. They represent assertions or statements. There is a hierarchy of associations and the root of all associations is the "Association" class. (example: GeneToDiseaseAssociation). 
The association connects a "subject" node and an "object" node via a "relation" prperty. Every association can have properties and some of them can have additional properties that are unique.
The nature of the association depends upon the value that is in the relation property.

Predicates are high-level relationships. They are used as predicate in a statement. Many predicates can be used with multiple kinds of associations.

The entirety of the model is defined in the yaml (considered as the source of truth) and then by using a biolinkML: a package or meta modeling framework to create a documentation , python data classes, json schema, RDF/OWL..

The modeling language provides the following idioms, - Class definition : Used to define classes - Slot definition : Used to define class properties - Type definition : Used to define data types - Schema definition : Used to define properties of the model itself

To that end, Biolink Model makes use of LinkML (the inked open data modeling framework) for defining the various semantics of the model.

For more information : https://biolink.github.io/biolink-model/understanding-the-model/

## Structure of the Model

The model itself is organized using linkML Class definition (class), Slot definition (slot), Type definition (type) and Schema definition.

At a glance the structure is as follows, **- Classes - Entities - Associations - Mixins - Slots - Predicates - Node Properties - Edge Properties - Types**

### Classes: 
A class can be an entity or an association. It can have one or more slots. Within the Biolink Model there are two hierarchies of classes: - Named Things - Associations (Named Things are disjoint from Associations) But they do share a common ancestor class: entity.
Named Things are classes that represent real world entities such as genes, diseases whereas associations are classes that represent an assertion or statement.

In general, Associations have three main properties (or slots): 
* subject: the subject of the association
* predicate: the predicate or relationship between the subject and the object of the association
* object: the object of the association These three properties (or slots) define what Biolink calls a "core triple".

Subjects and objects are always classes in the Biolink Model that are descendants of "biolink:NamedThing" and represent core biological, chemical, and biomedical concepts





<img width="686" height="353" alt="onion" src="https://github.com/user-attachments/assets/77c5d63f-8005-408f-8a7d-c32ed0ff6ebd" />

(figure from https://biolink.github.io/biolink-model/understanding-the-model/ )


Together, the subject, predicate, object, and optional qualifier(s) comprise the full semantics of the statement that an Association puts forth as true (i.e. its ‘S-P-O-Q’ semantics). Association objects may also include slots to hold Metadata about this core statement - primarily information about the provenance and evidence supporting it - but unlike qualifiers, this metadata does not contribute to the meaning of the core Statement itself. Using these qualifier and metadata elements together, we can build Associations with many possible ‘layers’ of complexity.

### Mixins

Mixins are defined as a way of encouraging reuse of specific slots (properties) while ensuring a clear inheritance chain. Mixins are used to extend the properties (or slots) of a class, without changing its position in the class hierarchy

### Slots

In Biolink Model, slots represent properties that a class or an association can have. In Biolink Model slots are used to represent - Predicates - Node Properties - Edge Properties 

### Predicates

In a graph formalism, predicates are relationships that link two instances.

### Node Properties

Node properties are slots that an entity class (i.e, a node) can have. The root of all node properties is biolink:node_property slot.

### Edge Properties

Edge properties are slots that an association class (i.e., an edge) can have. The root of all edge properties is association slot slot.

### Types 

In Biolink Model we have several data types: string /integer /uriorcurie/ float/ boolean/ iri type.
It is also possible to define custom data types using the modeling language.

#### More basic components (slots):
- *exact_mappings:* A list of terms from different schemas or terminology systems that have identical meaning.
- *aliases:* Alternate names/labels for the element. These do not alter the semantics of the schema, but may be useful to support search and alignment.
- *is_a:* A primary parent class or slot from which inheritable metaslots are propagated from. 
- *range:* defines the type of the object of the slot.
- *domain:* defines the type of the subject of the slot.
  
#### Interpreting a Fully Qualified Edge
see example in https://biolink.github.io/biolink-model/reading-a-qualifier-based-statement/


## Installation
```bash
pip install biolink-model
```
Additional functionality is available through extras:

Extra	Adds	Use it for
- scripts	(linkml, rdflib, curies):	The model generation/maintenance scripts under src/biolink_model/scripts/ (invoked via the Makefile)
- docs (mkdocs, mkdocs-material, mkdocs-mermaid2-plugin): 	Building the documentation site
- all: Everything
  
```bash
pip install "biolink-model[scripts]"    # generation/maintenance scripts
pip install "biolink-model[all]"        # everything
```
## Curating the Biolink Model: adding an entity class , an association class, a predicate and  properties

Within Translator, there is weekly data modeling calls and help desk set up for users. 

https://biolink.github.io/biolink-model/curating-the-model/

## Using the LinkML Modeling Language

**How to use most of the slots:**  https://biolink.github.io/biolink-model/using-the-modeling-language/



## Suite of tools for working with Biolink MODEL

**- biolinkML :** the meta modeling framework for building the Biolink Model from the YAML. It generates JSON Schema, python da  tables, Java classes, GraphQL, JSON-LD context, RDF Turtle, OWL, Shape Expressions (ShEx)

**- biolink-model-toolkit:** a utility for working with the Biolink Model. It's a python API for working with the Biolink Model. It provides convenience methods for querying the model.

**- KGX:** a knowledge graph exchange tool for merging, building and validating KGs. It's a python library and set of command line utilities for exchanging KGs that conform to or are aligned to the Biolink Model 

## References

- BioLink Model - standardizing knowledge graphs and making them interoperable - Deepak Unni - OBF: BOSC - ISMB/ECCB 2019: https://www.youtube.com/watch?v=8iM-WHW6zTA

- Slides: Presentation: https://bit.ly/biolink-model-workshop...
- Biolinkml github : https://github.com/biolink/biolinkml
- Biolink model toolkit: https://github.com/biolink/biolink-mo...
- Biolink KGX: https://github.com/biolink/kgx














