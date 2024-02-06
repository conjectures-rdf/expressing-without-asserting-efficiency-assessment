# EWA-efficiency

This repository contains all files to evaluate Expressing Without Asserting (EWA) approaches in RDF. 

## Data Acquisition, scaling and conversion

The dataset on which these experiments have been run is composed as follows and has been named D3:

- **Art**: A thematic set of claims about 300k artwork entities in Wikidata (i.e., painting, manuscripts, books). This corresponds to about 10% of all artwork entities currently present inside Wikidata.
- **Random**: after considerable deliberation, we concluded that adding some kind of entropy to the dataset would make it more representative. This dataset contains the claims of 300k Wikidata random entities.
- **Dummy**: a selection of dummy statements regarding the artwork attributions (represented by the property `wdt:P50` and `wdt:P170` and including from 1 to 4 authors in each claim and the source of the claim) and artworks locations (represented by the property `wdt:P276`, including 1 possible location, time constraints and source) has been created. Those new statements contain dummy arbitrary information ranked as deprecated and therefore non-asserted to represent alternative or historical claims to those contained in Art dataset. This design choice was made to increase the number of conjectural statements in the final dataset.

An excellent way to evaluate an algorithm’s performance is to observe how it responds to variations in input size [@Orlandi2021BenchmarkingRM]. We started by downloading the whole subset of artwork entities, related individuals (basically, attributed authors), and locations. This dataset, called D4, is composed of about 3,5 million artwork entities and 188 thousand related entities (humans and locations). We have not used this dataset for our comparison due to the excessive number of timeouts in many of the queries and methods we used. Thus we scaled the dataset logarithmically in three further sizes:

- Dataset D3: D3 is obtained by extracting one-tenth of the data in D4 (D3 = D4/10).
- Dataset D2: D2 is obtained by extracting one-tenth of the data in D3 (D2 = D3/10).
- Dataset D1: D1 is obtained by extracting one-tenth of the data in D2 (D1 = D2/10).

We then surveyed the state of the art regarding reification methods to express without asserting and selected a set of methods for our analysis: Singleton properties [@nguyen2014reification], Named graphs[@carroll2005named] (using Wikidata rankings to decide whether a triple is asserted or not), Wikidata[@erxleben_introducing_2014], and the recent RDF-star[@hartig2017foundations] approach. We converted Wikidata JSON files into the six selected reification methods through automatic scripts. In table \ref{tab:datasets} we provide some data about our datasets. At the end of this process, we obtained 18 new method-specific datasets. In other words, for each dataset $Dn, \; n \in [1, 3]$, we constructed the following datasets:

\begin{comment}
    
\begin{itemize}
    \item \textbf{Dn-Wikidata}: serialization: Turtle, reification: Wikidata, EWA: yes.
    \item \textbf{Dn-rdfStar}: serialisation: Turtle, reification: RDF-star, EWA: yes.
    \item \textbf{Dn-conjStrong}: serialization: TriG, reification: Conjectures strong form, EWA: yes.
    \item \textbf{Dn-nGraphs}: serialization: TriG, reification: Named graphs, EWA: no, but can be achieved via rankings.
    \item \textbf{Dn-conjWeak}: serialization: TriG, reification: Conjectures weak form, EWA: yes.
    \item \textbf{Dn-Singleton}: serialization: Turtle, reification: Singleton Property, EWA: yes.
\end{itemize}
\end{comment}

| **name**           | **Serialization** | **Reification**             | **EWA**         | **# RDF stmts in D3** |
|-------------------|-------------------|-----------------------------|-----------------|-----------------------|
| Dn-Wikidata       | Turtle            | Wikidata                    | yes             | 66,768,937           |
| Dn-rdfStar        | Turtle            | RDF-star                    | yes             | 29,779,850           |
| Dn-conjStrong     | TriG              | Conjectures - strong form   | yes             | 29,058,944           |
| Dn-nGraphs        | TriG              | Named graphs                | via ranking     | 28,896,268           |
| Dn-conjWeak       | TriG              | Conjectures - weak form     | yes             | 29,199,650           |
| Dn-Singleton      | Turtle            | Singleton properties        | yes             | 55,325,270           |


### Conversion rationale
In Wikidata, assertion or non assertion of claims is strictly dependent from their rankings. 

For example, the triples (1)```wd:Q10743 wdt:P214 "249422654"``` and (2)```wd:Q10743 wdt:P214 "315523483"``` share the same subject-predicate values, but differ wrt their objects. 

- If both triples (1 and 2) are ranked as Normal, they are both asserted.
- If both triples (1 and 2) are ranked as Preferred, they are both asserted.
- If both triples (1 and 2) are ranked as Deprecated, they are both non-asserted.
- If triple (1) is ranked as Preferred and triple (2) is ranked as Normal, the first (1) is asserted and the second (2) is non-asserted. 
- If triple (1) is ranked as Deprecated and triple (2) is ranked as Normal, the first (1) in non-asserted and the second (2) is asserted. 
- If triple (1) is ranked as Deprecated and triples (2) is ranked as Preferred, the first (1) is non-asserted and the second (2) is asserted. 

### Additional materials
- In folder ```handlebars_templates``` has been saved all templates to convert jsons into RDF with https://www.fabiovitali.it/wikidataconverter/
- In folder ```handlebars_templates_fake``` has been saved all templates to convert fake jsons (Dataset C) into RDF https://www.fabiovitali.it/wikidataconverter/
- In folder ```handlebars_templates``` you can find an additional set of helpers called ```helper.js```, this is meant to be use in data conversions since it reproduces the assertion - non assertion of the statements in the json files (a more in the depth explanation of the topic is in the section above).

### Converting JSON files via Wikidata Converter App
The downloaded json files from Wikidata can be trasformed into RDF format with the online converter 
- Download the application from  [LINK AL COVERTER AGGIORNATO].
- Start the application by simply starting node with the command ```node app.js```, the interface will be available in your browser at port ```3000```.
- In the interface, upload the templates (available in folder ```handlebars_templates``` and ```handlebars_templates_fake```) or fill the dedicated forms.
- Use "Bulk convert" function to upload a .zip archive containing all jsons. 
    - Note. Do not upload a .zip file grater than 2GB. 
    - Note 2. If the process stops, allocate more RAM space in the cmd with the command ```node --max-old-space-size=12288 app.js``` to run again the application. 
- A .zip folder will be automatically downloaded. This archive contains all RDF files converted against your chosen templates. 

# Example output RDF files out of handlebars templates
A conversion test has been run agaist the templates. In the folder ```conversion_test``` can be found input and output data. Each output RDF dataset has been validated with Apache Jena Fuseki. Below a summary for each dataset applied against each model:

| ** D1 **             | Upload time (ms) | Total Triples |  Query Time (ms) |  Query                                       | Queried Triples  |
|----------------------|------------------|---------------|------------------|----------------------------------------------|------------------|
| Wikidata Statement   |   18,904         |  853,028      | 32,860           | SELECT * WHERE {?s ?p ?o}                    |   751,332        |
| Singleton Properties |   12,034         |  695,022      | 12,375           | SELECT * WHERE {?s ?p ?o}                    |   334,955        |
| Named Graphs         |   15,428         |  364,150      | 6,901            | SELECT * WHERE {?s ?p ?o}                    |   184,813        |
| RDF-star             |   12,556         |  379,010      | 7,985            | SELECT * WHERE {<< ?s ?p ?o >>} ?p1 ?o1      |   163,975        |
| Conjectures          |   13,419         |  369,527      | 8,962            | SELECT * WHERE {?s ?p ?o}                    |   184,813        |

| ** D2 **             | Upload time (ms) | Total Triples |  Query Time (ms) |  Query                                       | Queried Triples  |
|----------------------|------------------|---------------|------------------|----------------------------------------------|------------------|
| Wikidata Statement   |  172.638         |  11,337,988   | 25.975           | SELECT * WHERE {?s ?p ?o} LIMIT 1000000      |    1000000       |         
| Singleton Properties |  104.060         |   9,229,518   | 17.282           | SELECT * WHERE {?s ?p ?o} LIMIT 1000000      |    1000000       |
| Named Graphs         |  116.632         |   4,742,770** | 27.431           | SELECT * WHERE {?s ?p ?o} LIMIT 1000000      |    1000000       |
| RDF-star             |   66.617         |   5,012,578   | 43.739           | SELECT * WHERE { << ?s ?p ?o >> ?p1 ?o1}     |    1000000       |
| Conjectures          |  119.180         |   4,810,697** | 26.988           | SELECT * WHERE {?s ?p ?o} LIMIT 1000000      |    1000000       |

* The _Query Time (ms)_ has been calculated over the query: ```SELECT * WHERE {?s ?p ?o}```
** Total number of Quads

Each converted dataset is exemplified below with two different examples:
1) The first represents two statements (_Germany native label is Bundesrepublik Deutschland_ and _Germany native label is Deutschland_) both ranked as normal, and then equally asserted.
2) The second represents three statements (_Germany has diplomatic relation with Taiwan_, _Germany has diplomatic relation with Bhutan_ (unconfirmed statement), _Germany has diplomatic relation with Cape Verde_) respectively rankes as normal (non asserted), deprecated (non asserted), preferred (asserted)

### Wikidata 

```
wd:Q183 wdt:P1705 "Bundesrepublik Deutschland"@de.
wd:Q183 p:P1705 s:Q183-d657d418-4a25-98d6-5180-a3659a11fbcd .
s:Q183-d657d418-4a25-98d6-5180-a3659a11fbcd a wikibase:Statement;                                  
    ps:P1705 "Bundesrepublik Deutschland"@de;
    wikibase:rank wikibase:NormalRank.                                   
    
wd:Q183 wdt:P1705 "Deutschland"@de.      
wd:Q183 p:P1705 s:Q183$E2A638D7-78B7-424D-9F63-AF49F5DCAE84 .
s:Q183-E2A638D7-78B7-424D-9F63-AF49F5DCAE84 a wikibase:Statement; 
    ps:P1705 "Deutschland"@de;
    wikibase:rank wikibase:NormalRank.
```    
```
wd:Q183 p:P530 s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA .
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA a wikibase:Statement;                                     
    pq:P805 wd:Q15910813;
    pq:P582 "1972-00-00T00:00:00Z"^^xsd:dateTime;
    pq:P2241 wd:Q26256296;
    ps:P530 wd:Q865;
    wikibase:rank wikibase:NormalRank.

wd:Q183 p:P530 s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 .
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 a wikibase:Statement; 
    pq:P805 wd:Q1201896;
    pq:P2241 wd:Q28831311;
    ps:P530 wd:Q917;
    wikibase:rank wikibase:DeprecatedRank.
     
wd:Q183 wdt:P530 wd:Q1011.
wd:Q183 p:P530 s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 .
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 a wikibase:Statement; 
    pq:P805 wd:Q28498636;
    pq:P531 wd:Q58003162;
    ps:P530 wd:Q1011;
    wikibase:rank wikibase:PreferredRank.
```

### Named Graphs
Note: With named graphs all statements are asserted.  
Note2: Since Named Graphs allows for statements groupings, when all statements are ranked as Normal (or all has the same qualifiers) as the case below, they can be grouped in the same graph without changing any statement meaning.

```
GRAPH s:Q183-d657d418-4a25-98d6-5180-a3659a11fbcd { 
    wd:Q183 wdt:P1705 "Bundesrepublik Deutschland"@de 
    wd:Q183 wdt:P1705 "Deutschland"@de 
}
s:Q183-d657d418-4a25-98d6-5180-a3659a11fbcd wikibase:rank wikibase:NormalRank.
```
```
GRAPH s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA { 
wd:Q183 wdt:P530 wd:Q865 
}
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA pq:P805 wd:Q15910813.
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA pq:P582 "1972-00-00T00:00:00Z"^^xsd:dateTime.
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA pq:P2241 wd:Q26256296.
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA wikibase:rank wikibase:NormalRank.

GRAPH s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 { 
    wd:Q183 wdt:P530 wd:Q917 
}
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 pq:P805 wd:Q1201896.
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 pq:P2241 wd:Q28831311.
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 wikibase:rank wikibase:DeprecatedRank.

GRAPH s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 { 
    wd:Q183 wdt:P530 wd:Q1011 								
}
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 pq:P805 wd:Q28498636.
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 pq:P531 wd:Q58003162.
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 wikibase:rank wikibase:PreferredRank.

```

### Singleton Properties
```
wd:Q183 wdt:P1705 "Bundesrepublik Deutschland"@de.
sng:P417-0 sng:singletonPropertyOf wdt:P417  ;
sng:P1705-0 sng:singletonPropertyOf wdt:P1705 ;
    wikibase:rank wikibase:NormalRank.
 
wd:Q183 wdt:P1705 "Deutschland"@de.
wd:Q183 sng:P1705-1 "Deutschland"@de.
sng:P1705-1 sng:singletonPropertyOf wdt:P1705  ;
    wikibase:rank wikibase:NormalRank.
```
```
wd:Q183 sng:P530-136 wd:Q865.
sng:P530-136 sng:singletonPropertyOf wdt:P530  ;
    pq:P805 wd:Q15910813;
    pq:P582 "1972-00-00T00:00:00Z"^^xsd:dateTime;
    pq:P2241 wd:Q26256296;
    wikibase:rank wikibase:NormalRank.

wd:Q183 sng:P530-132 wd:Q917.
sng:P530-132 sng:singletonPropertyOf wdt:P530  ;
    pq:P805 wd:Q1201896;
    pq:P2241 wd:Q28831311;
    wikibase:rank wikibase:DeprecatedRank.

wd:Q183 sng:P530-133 wd:Q1011.
sng:P530-133 sng:singletonPropertyOf wdt:P530  ;
sng:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 sng:singletonPropertyOf wdt:P530 .
    pq:P805 wd:Q28498636;
    pq:P531 wd:Q58003162;
    wikibase:rank wikibase:PreferredRank.
```

### RDF-star (RDF 1.1 syntax extension)
```
wd:Q183 wdt:P1705 "Bundesrepublik Deutschland"@de.
<<wd:Q183 wdt:P1705 "Bundesrepublik Deutschland">>
     wikibase:rank wikibase:NormalRank.
     
wd:Q183 wdt:P1705 "Deutschland"@de.
<<wd:Q183 wdt:P1705 "Deutschland">>
     wikibase:rank wikibase:NormalRank.
```
```
<<wd:Q183 wdt:P530 wd:Q865>>
    pq:P805 wd:Q15910813;
    pq:P582 "1972-00-00T00:00:00Z"^^xsd:dateTime;
    pq:P2241 wd:Q26256296;
    wikibase:rank wikibase:NormalRank.

<<wd:Q183 wdt:P530 wd:Q917>>
    pq:P805 wd:Q1201896;
    pq:P2241 wd:Q28831311;
    wikibase:rank wikibase:DeprecatedRank.
     
wd:Q183 wdt:P530 wd:Q1011.
<<wd:Q183 wdt:P530 wd:Q1011>>
    pq:P805 wd:Q28498636;
    pq:P531 wd:Q58003162;
    wikibase:rank wikibase:PreferredRank.
```

### Conjectures (weak form)
Note: Since conjectures allows for statements groupings (inheriting it from Named Graphs), when all statements are ranked as Normal (or all has the same qualifiers) as the case below, they can be grouped in the same graph without changing any statement meaning. 
```
GRAPH s:Q183-d657d418-4a25-98d6-5180-a3659a11fbcd  {
wd:Q183 wdt:P1705 "Bundesrepublik Deutschland"@de.
wd:Q183 wdt:P1705 "Deutschland"@de.
}
s:Q183-d657d418-4a25-98d6-5180-a3659a11fbcd wikibase:rank wikibase:NormalRank.                        
```
```
GRAPH s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA  {
	wd:Q183 <http://w3id.org/conj-wikidata-test/conj/Q183P530136/P530> wd:Q865.
	<http://w3id.org/conj-wikidata-test/conj/Q183P530136/P530> conj:isAConjecturalFormOf wd:P530 .
}
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA pq:P805 wd:Q15910813.
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA pq:P582 "1972-00-00T00:00:00Z"^^xsd:dateTime.
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA pq:P2241 wd:Q26256296.
s:Q183-DF432913-CEBA-49ED-BCA4-7214957E6CDA wikibase:rank wikibase:NormalRank.   

GRAPH s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6  {
	wd:Q183 <http://w3id.org/conj-wikidata-test/conj/Q183P530132/P530> wd:Q917.
	<http://w3id.org/conj-wikidata-test/conj/Q183P530132/P530> conj:isAConjecturalFormOf wd:P530 .
}
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 pq:P805 wd:Q1201896.
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 pq:P2241 wd:Q28831311.
s:Q183-a6aa383f-4c30-79bf-0767-dcf4ea80f8d6 wikibase:rank wikibase:DeprecatedRank.   

GRAPH s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1  {
        wd:Q183 wdt:P530 wd:Q1011.
}

GRAPH s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1  {
        wd:Q183 <http://w3id.org/conj-wikidata-test/conj/Q183P530133/P530> wd:Q1011.
	<http://w3id.org/conj-wikidata-test/conj/Q183P530133/P530> conj:isAConjecturalFormOf wd:P530 .  
}      

GRAPH s:collapseOfQ183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1{
        wd:Q183 wdt:P530 wd:Q1011.
        s:collapseOfQ183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 conj:collapses s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 
}
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 pq:P805 wd:Q28498636.
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 pq:P531 wd:Q58003162.
s:Q183-0B26503A-A8BF-4B40-9F0A-CAE242AE03A1 wikibase:rank wikibase:PreferredRank.   
```


To run the experiments take the following steps:
- Run locally one of the two available dockers:
  - ```docker push valentinamomo/conjectures-graphdb-ewa-rtr:tagname```: This docker is **ready to run (RTR)** and can be mounted locally in few minutes. It contains a customised instance of GraphDB to parse and read Conjectures and 10 preloaded datasets to compare its efficiency with concurrent approaches. In particular, this instance contains Wikidata statements, RDF-star, Named Graphs, Conjectures in Weak form, Conjectures in strong form each of which comes in two sizes (D1 and D2). It requires 20GB of free memory space to be used.
  - ```tbd```: This docker is **slow to run (STR)** and can be mounted locally in some hours. It contains a customised instance of GraphDB to parse and read Conjectures and 18 preloaded datasets to compare its efficiency with concurrent approaches. In particular, this instance contains Wikidata statements, RDF-star, Named Graphs, Singleton Properties, Conjectures in Weak form, Conjectures in strong form each of which comes in three sizes (D1, D2 and D3). It requires 0.5T of free memory space to be used.
  - Modifications of GraphDB instances in the dockers are stored in ```conjectures-extension-graphdb```
  
- Run locally the code provided in ```XXX``` and locate the ```queries``` in the same folder.
  - ```queries``` contains the set of queries designed for this experiment and the code to run them 
  - ```query_exectution_response``` contains partial and final results of query runs which come from this test attempt. 
