---
layout: post
title: "Which text-embedder best fits your language?"
excerpt: "How not to get lost in multidimensional space"
author: francesco.zubani

categories: [ Data Science, Machine Learning, Enmbeddings, Artificial Intelligence, Retrieval Augmented Generation, RAG]
image: assets/images//comparing_embedders/cover.png
---

<style>
    iframe{
        border: 0px;
        width: 130em;
    }
</style>

<center>
    <em>Cover image by author.</em>
</center>
<br>

### Can LLMs discriminate against us?

This is a deliberately vague and somewhat provocative question, but let me explain.

Speaking about AI, we all know that most *large language models* work better in English.

The same goes for ***embedders***, though multi-language models are a good alternative.

But let's say we have a database of documents written in another language, and we want to find out which one is the most suitable for our purpose.

How can we achieve that goal?

### Our idea, in synthesis

Let's take **some topics**, with **topic related sentences** (to speed up the process, we <ins>asked an LLM to generate them</ins> for us).

Then, with each model, we generate the embeddings for every topic and sentence, and calculate:

1. The distance of **every sentences** from a **every topics**

2. The distance of **each sentence related to a topic** from **that topic itself**

At this point, we can answer these questions:

1. How far is <ins>**each sentence** from **a specific topic**</ins>?
   
<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/barchart.png">
    <br>
    <em> Example of bar chart visualization (Image by author) </em>
</center>

2. Which is the average distance of each sentence from the topic it should be related to?

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/average.png">
    <br>
    <em> Example average and standard deviation (Image by author) </em>
</center>

For models that perform better on a given language, we expect that:
1. Sentences related to a topic have a shorter distance from their respective topics
2. The average distance of all related sentences from their topic will be lower
3. There is less variance in distances, so the standard deviation will be lower too

### Our method: a little in-depth


Basically, our method consists of five steps:

1. Creation of samples (sentences relating to certain categories)
2. Choice/download of the model/models we want to test
3. Generation of embeddings (of samples and categories, for each model we want to test)
4. Storage of embeddings in a vector database (we used Qdrant)
5. Calculation of distances and final evaluation

#### STEP 1: Creating samples

Let's create one or more sample with the following structure:

```json
{
    "Topic": [
        "topic-related_sentence_1",
        "topic-related_sentence_2",
        // ...
    ]
}
```

  Example:
```json
    {
        "Tecnologia": [
        "Il computer è uno strumento.",
        "I telefoni moderni sono versatili.",
        "La realtà virtuale è immersiva."
    ],
    "Cucina": [
        "Gli chef creano piatti deliziosi.",
        "I dolci sono molto apprezzati.",
        "Il forno è molto utile."
    ]
    }
```

#### STEP 2: Downloading models

After conducting some research, we compiled a list of effective embedders for Italian:

- [Anita](https://huggingface.co/DeepMount00/Anita)
- [cross_encoder_italian_bert_stsb](https://huggingface.co/nickprock/cross-encoder-italian-bert-stsb)
- [mmarco_bert_base_italian_uncased](https://huggingface.co/nickprock/mmarco-bert-base-italian-uncased)
- [multi_e5](https://huggingface.co/intfloat/multilingual-e5-large)
- [sentence_bert_base_italian_xxl_uncased](https://huggingface.co/nickprock/sentence-bert-base-italian-xxl-uncased)
- [stsbm_sentence_flare_it](https://huggingface.co/nickprock/stsbm-sentence-flare-it)
- [italian_ner_xxl](https://huggingface.co/DeepMount00/Italian_NER_XXL)
- [stsb_multi_mt](https://huggingface.co/abhijithneilabraham/stsb_multi_mt_distilbert-base-uncased)
- [multilingual_minilm](https://huggingface.co/microsoft/Multilingual-MiniLM-L12-H384)
- [bert_base_italian_xxl_uncased](https://huggingface.co/dbmdz/bert-base-italian-xxl-uncased)
- [umberto_commoncrawl_cased](https://huggingface.co/Musixmatch/umberto-commoncrawl-cased-v1)
- [paraphrase_multilingual_mpnet_v2](https://huggingface.co/sentence-transformers/paraphrase-multilingual-mpnet-base-v2)


Subsequently, we used [**transformer**](https://huggingface.co/docs/hub/transformers) library to download these models:

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/download.gif">
    <br>
    <em> Downloading and instantiating embedders (Screencast by author) </em>
</center>
<br>

#### STEP 3: Creating the embeddings

We create embeddings for every topic/sentence, iterating over samples in samples' directory.

To achieve this, we used [**langchain_huggingface**](https://python.langchain.com/docs/integrations/providers/huggingface/) library:

```python
    from langchain_huggingface import HuggingFaceEmbeddings

    class EmbeddingsLocalModel(Embedder):
        def __init__(self, local_model: HuggingFaceEmbeddings) -> None:
            self.local_model = local_model

        def embed(self, texts: Union[str, list[str]]) -> list[list[float]]:
            if isinstance(texts, str):
                texts = [texts]
            return self.local_model.embed_documents(texts)
```

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/embeddings.gif" width="1100">
    <br>
    <em> Generating embeddings (Screencast by author) </em>
</center>

#### STEP 4: Storing to Qdrant

Next, we store these embeddings in [**Qdrant**](https://qdrant.tech/documentation/overview/) using its [Python client](https://python-client.qdrant.tech/).

Here’s an example setup:

```python
    from qdrant_client import QdrantClient

    class QdrantHandler:
        def __init__(self):
            self.client = QdrantClient(host='your_host', port='your_port')

        def create_collection(self, model_name, vector_size):
        # Implementation ...

        def upsert_points(self, collection_name, embeddings_dict):
        # Implementation ...
```

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/qdrant.gif" width="1100">
    <em> Creating collections and upserting points (Screencast by author) </em>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/collections.gif" width="1100">
    <em> Qdrant points visualization (Screencast by author) </em>
    <br>
</center>

#### STEP 5: Calculating distances and evaluating results

But how does evaluation process work?

1. **Querying Qdrant**
    * We fetch stored points from a specified collection using a method that scrolls through all items:

    ```python
        from qdrant_client import QdrantClient
        
    def _retrieve_points(self, collection_name: str):
        points = []
        response, next_page_offset = self.client.scroll(collection_name, with_payload=True, limit=100, with_vectors=True)
        while response:
            points.extend(response)
            response, next_page_offset = self.client.scroll(collection_name, with_payload=True, limit=100, offset=next_page_offset, with_vectors=True)
        return points
    ```
2. **Calculating Distances**
    * For each topic, we compute the distance between the sentence and the topic embeddings using various metrics:

    ```python
        from scipy.spatial.distance import cosine, euclidean, cityblock

        def _compute_distance(self, vector1, vector2, metric: str):
            if metric == "cosine":
                return cosine(vector1, vector2)
            elif metric == "euclidean":
                return euclidean(vector1, vector2)
            elif metric == "dot":
                return -np.dot(vector1, vector2)
            elif metric == "manhattan":
                return cityblock(vector1, vector2)
            return None
    ```

    * Then, we sort the results in ascending order: for each topic, nearer sentences come first.

3. **Calculating Averages**

* Finally:

    * I calculate the average distances and standard deviations for each category.
    
    * By iterating over the distance metrics and models, I conduct evaluations for each combination and store the sorted results accordingly.

#### STEP 6: Generating plots

This final step may be useful for visualizing the results of our evaluations.

Using [**matplotlib**](https://matplotlib.org/stable/users/explain/quick_start.html) and [**seaborn**](https://seaborn.pydata.org/), we implemented a class to generate heatmaps and barcharts from the evaluation results.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/plots.gif">
    <br>
    <em> Generating plots (Screencast by author) </em>
</center>

These visualizations help in understanding the performance of different embedders at a glance.

Additionally, we built a small GUI using the [**streamlit**](https://streamlit.io/) framework, to facilitate interaction with the visual data.

Here’s the final result:

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders/viz.gif">
    <br>
    <em> Interactive visualization (Screencast by author) </em>
</center>
