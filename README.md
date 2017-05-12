# T-Rex : A Large Scale Alignment of Natural Language with Knowledge Base Triples

Paper Under Submission.

## Introduction

Several studies have constructed datasets where alignments between knowledge base triples and free text have been made, using them for several independent tasks, such as Relation Extraction, Knowledge base population, Relation Discovery, NLG...

But where the others datasets have a small number of documents (*TAC-KBP*), only have the relations without the original documents (*FB15K-237*), have a little amount of relations (*Google-RE*), we present a dataset containing large-scale high-quality alignments between DBpedia abstracts and Wikidata triples, **_T-REx_**.

## Size

**_T-REx_** leverages 11 million triple alignments out of 3.09 million DBpedia abstracts (6.2 million sentences), which makes it two orders of magnitude larger than the largest available alignments to the community.

| Dataset                  | Documents / Format      | unique predicates | Aligned Triples | Availability        |
|:------------------------:|:-----------------------:|:-----------------:|:---------------:|:-------------------:|
| NYT-FB (yao et al. 2011) | 1.8M Sent.              | 258               | 39K             | partially available |
| TAC KBP                  | 90K sent.               | 41                | 122K            | closed              |
| FB15K-237                | 2.7 M textual-relations | 237               | 2.7M            | publicly available  |
| Wikireadings             | 4.7M Articles           | 884               | n.a.            | publicly available  |
| Google-RE                | 60K Sent.               | 5                 | 60K             | publicly available  |

## Evaluation

