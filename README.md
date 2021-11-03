# Multilingual entity linking

The EMEBDDIA project aims to analyse documents in low-resourced languages. After recognising NEs in the documents, we disambiguate their entities to a KB. To have a large number of entities for each language, we built a KB and a dataset to train entity embeddings for each language of the project and, then, we used our entity disambiguation approach to link these entities for their corresponding language version KB.

## Building resources

Most works in the state of the art use the English version of Wikipedia as KB to disambiguate mentions. However, the English Wikipedia may contain fewer pages about persons, organisations, and locations about France and its culture than the French version of Wikipedia. This analysis is similar for the other languages of the EMBEDDIA project. Therefore, we build a KB for each EMBEDDIA language to have a richer KB for each language.

Each language's version of KB is created by following the steps:
- Retrieve the last language version of Wikipedia dump.
- Extract titles and ids of Wikipedia pages.
- Extract list of disambiguation pages and redirection pages.
- Calculate the probability that an entity is related to a mention based on the number of times that mention refers to that entity.

We also build a dataset to train entity embeddings for each language. In this case, we use the methodology used by Ganea and Hofmann (https://github.com/dalab/deep-ed) to create and train entities embeddings based on the Wikipedia dataset.

## Entity embeddings

Following the same idea described in Ganea and Hofmann (https://github.com/dalab/deep-ed), we collected word-entity (word $w$ and entity $e$) co-occurrence counts $(w,e)$ from two sources: (i) the canonical KB description page of the entity (e.g. entity’s Wikipedia page in our case), and (ii) the windows of fixed size surrounding mentions of the entity in an annotated corpus. These counts define a practical approximation of the above word-entity conditional distribution. These words are considered to be the "positive" distribution of entity-related words. Then, a sample of words is selected randomly to create a "negative" distribution of words that are unrelated to the entity $e$. The objective is to move positive word vectors closer to the embeddings of the entity $e$ and move the vectors of random words further away from the embeddings of the entity $e$ (more details in https://github.com/dalab/deep-ed).

## Entity disambiguation

For the entity disambiguation, our model is based on Kolitsas et al.'s work (https://github.com/dalab/end2end_neural_el) that is a neural end-to-end entity linking model. This model is interesting because we can analyse the entity linking and disambiguation with the same model. Besides, this end-to-end model does not require engineered features, making it easy to upgrade and extend to other languages.

The first step in the entity linking is to recognise all mentions in a document. Kolitsas et al. used an empirical probabilistic entity-map $p(e|m)$ (calculated from the Wikipedia corpora for each language) to analyse each span $m$ and select top entities that might be referred by this mention in $p(e|m)$.

Word and character embeddings are concatenated and fed into a BiLSTM to represent a document. This representation is used to project mentions of this document in a dimensional space with the same size of entity embeddings. Entity embedding is calculated separately for each entity using the following exponential model that approximates the empirical conditional word-entity distribution $\widehat{p}(w|e)$ obtained from co-occurrence counts.

In order to analyse long context dependencies of mentions, they used the attention model of GH that gives one context embedding per mention based on informative context words that are related to at least one of the candidate entities.
Next, the final local score for each mention is determined by the combination of the $\log p(e|m)$, the similarity between the analysed mention and each candidate entity embeddings, and the long-range context attention for this mention.
Finally, a top layer in the neural network promotes the coherence among disambiguated entities inside the same document.

## Post-processing filter 

For certain publication we have explored as well a post-processing filter. This tool can be found in the following Github repositiory:
https://github.com/EMBEDDIA/NEL_Filter

## How to Cite?

Please cite this work with the following publication:
```
@InProceedings{10.1007/978-3-030-64452-9_19,
  author="Linhares Pontes, Elvys
  and Cabrera-Diego, Luis Adri{\'a}n
  and Moreno, Jose G.
  and Boros, Emanuela
  and Hamdi, Ahmed
  and Sid{\`e}re, Nicolas
  and Coustaty, Micka{\"e}l
  and Doucet, Antoine",
  editor="Ishita, Emi
  and Pang, Natalie Lee San
  and Zhou, Lihong",
  title="Entity Linking for Historical Documents: Challenges and Solutions",
  booktitle="Digital Libraries at Times of Massive Societal Transition",
  year="2020",
  publisher="Springer International Publishing",
  address="Cham",
  pages="215--231",
  doi="10.1007/978-3-030-64452-9_19"
  isbn="978-3-030-64452-9"
}
``` 

# Parent project

This work is is result of the European Union H2020 Project [Embeddia](http://embeddia.eu/). Embeddia is a project that creates NLP tools that focuses on European under-represented languages and that has for objective to improve the accessibility of these tools to the general public and to media enterprises. Visit [Embeddia's Github](https://github.com/orgs/EMBEDDIA/) to discover more NLP tools and models created within this project.
