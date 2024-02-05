# EWA-efficiency

This repository contains all files to evaluate Expressing Without Asserting (EWA) approaches in RDF. 

To run the experiments take the following steps:
- Run locally one of the two available dockers:
-- ```docker push valentinamomo/conjectures-graphdb-ewa-rtr:tagname``` - This docker is **ready to run (RTR)** and can be mounted locally in few minutes. It contains a customised instance of GraphDB to parse and read Conjectures and 10 preloaded datasets to compare its efficiency with concurrent approaches. In particular, this instance contains Wikidata statements, RDF-star, Named Graphs, Conjectures in Weak form, Conjectures in strong form each of which comes in two sizes (D1 and D2). It requires 20GB of free memory space to be used.
-- ```tbd``` - This docker is **slow to run (STR)** and can be mounted locally in some hours. It contains a customised instance of GraphDB to parse and read Conjectures and 18 preloaded datasets to compare its efficiency with concurrent approaches. In particular, this instance contains Wikidata statements, RDF-star, Named Graphs, Singleton Properties, Conjectures in Weak form, Conjectures in strong form each of which comes in three sizes (D1, D2 and D3).
--- Modifications of GraphDB instances in the dockers are stored in ``conjectures-extension-graphdb```
- Run locally the code provided in ```XXX``` and locate the ```queries``` in the same folder.
-- ```queries``` contains the set of queries designed for this experiment and the code to run them 
-- ```query_exectution_response``` contains partial and final results of query runs which come from this test attempt. 
