# T-Rex : A Large Scale Alignment of Natural Language with Knowledge Base Triples

Paper Under Submission.

## Introduction

Several datasets with alignments between knowledge base triples and free text have been built, for several independent tasks, such as Relation Extraction, Knowledge base population, Relation Discovery...

But where the others datasets have a small number of documents (*TAC-KBP*), only have the relations without the original documents (*FB15K-237*), have a little amount of relations (*Google-RE*), we present a dataset containing large-scale high-quality alignments between DBpedia abstracts and Wikidata triples, **_T-REx_**.

## Size

With 11 million triple alignments made from 3.09 million DBpedia abstracts (6.2 million sentences), **_T-REx_** is two orders of magnitude larger than the largest available alignments to the community.

| Dataset                  | Documents / Format      | Unique predicates | Aligned Triples | Availability           |
|:------------------------:|:-----------------------:|:-----------------:|:---------------:|:----------------------:|
| NYT-FB (yao et al. 2011) | 1.8M Sent.              | 258               | 39K             | partially available    |
| TAC KBP                  | 90K Sent.               | 41                | 122K            | closed                 |
| FB15K-237                | 2.7 M textual-relations | 237               | 2.7M            | publicly available     |
| Wikireadings             | 4.7M Articles           | 884               | n.a.            | publicly available     |
| Google-RE                | 60K Sent.               | 5                 | 60K             | publicly available     |
| **T-REx**                | **6.2M Sent.**          | **642**           | **11M**         | **publicly available** |

## How is it built ?
We use an alignment pipeline which contains a set of components to read documents, entity linking and extract triples.
We made it easy to use this pipeline over new text corpora and even plug your own annotator like Stanford NER Tagger, Babelfy, etc...
For the triples, we have three methodologies that all rely on the distant supervision assumption.
* The Nosubject aligner :
Assumes that sentences in the same paragraph have the same subject.
* The AllEnt aligner :
Take every pair of entities in the sentence and find matches in the knowledge base relations.
(Use also coreference resolution to solve the implicit mention of entities by finding all mentions of the main entity in the other paragraphs.)
* The SPO aligner :
Aligns triples only when the subject, the object **and** the predicate are found in the sentence (the predicate is extracted using a property linker)

![System architecture picture](./images/system.png "System architecture")      

## Evaluation
We derive a crowdsourcing experiment to evaluate the quality of our generated dataset.
We asked contributors to read the document and annotate each triple to be true only if the triple is explicitly mentioned in the given document.
To guarantee high quality annotations we manually annotated 100 documents and used them to filter out the bad contributors.

## Dataset Formats
Our dataset is available in [JSON format](./samples/sample-output.json) and [NIF format](./samples/sample-output-Nif.ttl).

# JSON
```
{
    "docid": "http://www.wikidata.org/entity/Q228",                     # Document ID
    "title": "Andorra",                                                 # Title of the document
    "text": "Andorra (/ænˈdɔːrə/; [ənˈdorə], [anˈdɔra]), officially the Principality of Andorra (Catalan: Principat d'Andorra), also called the Principality of the Valleys of Andorra (Catalan: Principat de les Valls d'Andorra), is a sovereign landlocked microstate in Southwestern Europe, located in the eastern Pyrenees mountains and bordered by Spain and France. Created under a charter in A.D. 988, the present Principality was formed in A.D. 1278. It is known as a principality as it is a monarchy headed by two Co-Princes – the Spanish/Roman Catholic Bishop of Urgell and the President of France. Andorra is the sixth-smallest nation in Europe, having an area of 468 km2 (181 sq mi) and a population of approximately 85,000. Its capital Andorra la Vella is the highest capital city in Europe, at an elevation of 1,023 metres (3,356 ft) above sea level. The official language is Catalan, although Spanish, Portuguese, and French are also commonly spoken. Andorra's tourism services an estimated 10.2 million visitors annually. It is not a member of the European Union, but the Euro is the de facto currency. It has been a member of the United Nations since 1993. In 2013, the people of Andorra had the highest life expectancy in the world at 81 years, according to The Lancet. Andorra entered service at 1970-01-22.",                                                                  # The whole text of the document
    "uri": "http://www.wikidata.org/entity/Q228",                       # URI of the item containing the main page
    "words_boundaries": [                                               # List of tuples (start, end) of each word in the document, start/end are character indices
      [
        0,
        7
      ],
      [
        8,
        10
      ]
    ]
    "sentences_boundaries": [                                           # List of tuples (start, end) of each sentence in the document, start/end are character indices
        [
            0,
            351
        ],
        [
            352,
            383
        ]
    ]
    "triples": [                                                        # List of triples that occur in the document
      {                                                                 # We opt of having them exclusive of other fields so they can be self contained and easy to process
        "sentence_id": 0,                                               # Integer showing which sentence does this triple lie in
        "subject": {                                                    # Class Entity (subject of the triple)
          "boundaries": [                                               # The boundaries of the subject's name in the paragraph
            0,
            7
          ],
          "surfaceform": "Andorra",                                     # The name of the entity
          "uri": "http://www.wikidata.org/entity/Q228",                 # The URI of the entity
          "annotator": "Wikidata_Spotlight_Entity_Linker"               # The annotator of the entity
        },
        "predicate": {                                                  # Class Entity (predicate of the triple)
          "boundaries": null,                                           # The boundaries of the property's name in the paragraph (null if the aligner can't bring it)
          "surfaceform": null,                                          # The name of the predicate (null if the aligner can't bring it)
          "uri": "http://www.wikidata.org/prop/direct/P37",             # The property URI
          "annotator": "NoSubject-Triple-aligner"                       # The annotator who made this triple
        },
        "object": {                                                     # Class Entity (object of the triple)
          "boundaries": [                                               # The boundaries of the entity's name
            84,
            91
          ],
          "surfaceform": "Catalan",                                     # The name of the entity
          "uri": "http://www.wikidata.org/entity/Q7026",                # The URI of the entity
          "annotator": "Wikidata_Spotlight_Entity_Linker"               # The annotator of the entity
        },
        "dependency_path": null,                                        # Lexicalized dependency path between sub and obj if exists or None (if not existing)
        "confidence": null,                                             # Confidence of annotation if possible
        "annotator": "NoSubject-Triple-aligner"                         # Annotator used to annotate this triple with the sentence
      }
    ]
    "entities": [                                                       # List of Entities   (Class Entity)
      {
        "boundaries": [                                                 # Tuple containing the start and the end of the surfaceform of the entity
          0,
          7
        ],
        "surfaceform": "Andorra",                                       # Name of the entity
        "uri": "http://www.wikidata.org/entity/Q228",                   # URI of the entity
        "annotator": "Wikidata_Spotlight_Entity_Linker"                 # The annotator used to detect this entity
      }
    ]
}
```

# NIF (ttl)
The NLP Interchange Format (NIF) is an RDF/OWL-based format that aims to achieve interoperability between Natural Language Processing (NLP) tools, language resources and annotations.

[NLP Interchange Format page](http://persistence.uni-leipzig.org/nlp2rdf/)
```
# Prefixes
@prefix ann: <http://triplr.dbpedia.org/resource/> .
@prefix wd: <http://www.wikidata.org/entity/> .
@prefix wdt: <http://www.wikidata.org/prop/direct/> .
@prefix nif: <http://ontology.neuinfo.org/NIF/Backend/nif_backend.owl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix itsrdf: <http://www.w3.org/2005/11/its/rdf#> .


#### MAIN PARAGRAPHS #####  
<http://www.wikidata.org/entity/Q228?nif=context>                                # Repeated for every document (Q228 is the document.pageuri)
   nif:beginIndex "0"^^xsd:nonNegativeInteger ;                                  # Beginning of the paragraph
   nif:endIndex "1306"^^xsd:nonNegativeInteger ;                                 # End of the paragraph
   nif:isString """Andorra (/ænˈdɔːrə/; [ənˈdorə], [anˈdɔra]), officially the Principality of Andorra (Catalan: Principat d'Andorra), also called the Principality of the Valleys of Andorra (Catalan: Principat de les Valls d'Andorra), is a sovereign landlocked microstate in Southwestern Europe, located in the eastern Pyrenees mountains and bordered by Spain and France. Created under a charter in A.D. 988, the present Principality was formed in A.D. 1278. It is known as a principality as it is a monarchy headed by two Co-Princes – the Spanish/Roman Catholic Bishop of Urgell and the President of France. Andorra is the sixth-smallest nation in Europe, having an area of 468 km2 (181 sq mi) and a population of approximately 85,000. Its capital Andorra la Vella is the highest capital city in Europe, at an elevation of 1,023 metres (3,356 ft) above sea level. The official language is Catalan, although Spanish, Portuguese, and French are also commonly spoken. Andorra's tourism services an estimated 10.2 million visitors annually. It is not a member of the European Union, but the Euro is the de facto currency. It has been a member of the United Nations since 1993. In 2013, the people of Andorra had the highest life expectancy in the world at 81 years, according to The Lancet. Andorra entered service at 1970-01-22. """;      # Whole text of the document
   nif:predLang <http://lexvo.org/id/iso639-3/eng> ;                             # The language
   a nif:Context .                                     

######## TRIPLES ALIGNED #############
# ...

# Triple 72 : 
ann:72 a nif:AnnotationUnit ;                                                    # Annotation unit with its number 
   nif:subject wd:Q228 ;                                                         # The subject
   nif:predicate wdt:P47 ;                                                       # The predicate
   nif:object wd:Q29 ;                                                           # The object
   rdfs:comment "SPOAligner" .                                                   # The aligner who made this triple

######## Entities per Triple ############

# Subject of the triple 72, printed if the annotator of the triple is the SPOAligner or the AllEntitiesAligner (SimpleAligner)

<http://www.wikidata.org/entity/Q228?nif=phrase&char=0,7>                        # Q228 refer to the URI of the document here, not to the entity
   nif:annotationUnit ann:annotation72 ;                                         # This means associated to Annotation 72
   nif:anchorOf "Andorra" ;                                                      # The name of the entity
   nif:beginIndex "0"^^xsd:nonNegativeInteger ;                                  # The beginning of the entity's name in the paragraph
   nif:endIndex "7"^^xsd:nonNegativeInteger ;                                    # The end of the entity's name in the paragraph
   nif:referenceContext <http://www.wikidata.org/entity/Q228?nif=context> ;      # The document where this was mentioned
   itsrdf:taIdentRef wd:Q228 ;                                                   # The entity URI
   a nif:Phrase ;                                                                
   rdfs:comment "Wikidata_Spotlight_Entity_Linker" .                             # The linker who made this annotation

# Predicate of the triple 72, printed if the annotator of the triple is the SPOAligner

<http://www.wikidata.org/entity/Q228?nif=phrase&char=322,333>                    # Q228 refer to the URI of the document here, not to the entity
   nif:annotationUnit ann:annotation72 ;                                         # This means associated to Annotation 72
   nif:anchorOf "bordered by" ;                                                  # The name of the predicate (removed if the aligner can't bring it)
   nif:beginIndex "322"^^xsd:nonNegativeInteger ;                                # The beginning of the property's name in the paragraph (removed if the aligner can't bring it)
   nif:endIndex "333"^^xsd:nonNegativeInteger ;                                  # The end of the property's name in the paragraph (removed if the aligner can't bring it)
   nif:referenceContext <http://www.wikidata.org/entity/Q228?nif=context> ;      # The document where this was mentioned
   itsrdf:taIdentRef wdt:P47 ;                                                   # The property URI
   a nif:Phrase ;                                                                
   rdfs:comment "Wikidata_Property_Linker" .                                     # The linker who made this annotation

# Object of the triple 72

<http://www.wikidata.org/entity/Q228?nif=phrase&char=334,339>                    # Q228 refer to the URI of the document here, not to the entity
   nif:annotationUnit ann:annotation72 ;                                         # This means associated to Annotation 72
   nif:anchorOf "Spain" ;                                                        # The name of the entity
   nif:beginIndex "334"^^xsd:nonNegativeInteger ;                                # The beginning of the entity's name in the paragraph
   nif:endIndex "339"^^xsd:nonNegativeInteger ;                                  # The end of the entity's name in the paragraph
   nif:referenceContext <http://www.wikidata.org/entity/Q228?nif=context> ;      # The document where this was mentioned
   itsrdf:taIdentRef wd:Q29 ;                                                    # The entity URI
   a nif:Phrase ;                                                                
   rdfs:comment "Wikidata_Spotlight_Entity_Linker" .                             # The linker who made this annotation

# ...
```

## Accessibility and Licensing:
**The full dump and the code for the alignment pipeline will be available after paper's acceptance.**

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

## Downloads
T-REx is in constant state of evolving. We work to integrate new aligners in the alignment pipeline whose output is available on the [Download page](http://hadyelsahar.github.io/t-rex/downloads).

## Contributors
Hady Elsahar
PhD. Student, Université de Lyon, Saint Etienne, France.
hady.elsahar@univ-st-etienne.fr

Pavlos Vougiouklis
PhD. Student, Electronics and Computer Science University of Southampton, Southampton, UK
pv1e13@ecs.soton.ac.uk

Arslen Remaci
Student Intern, Laboratoire Hubert Curien, Saint Etienne, France.
arslen.remaci@etu.univ-st-etienne.fr

## Acknowledgements
![Université Jean Monnet logo](./images/ujm.png "Université Jean Monnet")      ![Southampton University logo](./images/soton.png "Southampton University")

![EU](./images/eu.png "EU") 

WDAqua project is funded by the European Union‘s Horizon 2020 research and innovation programme under the Marie Skłodowska-Curie grant agreement No 642795.
