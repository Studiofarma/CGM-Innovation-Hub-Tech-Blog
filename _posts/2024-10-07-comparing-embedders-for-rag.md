---
layout: post
title: "Comparing Embedders for Retrieval Augmented Generation"
excerpt: "How not to get lost in multidimensional space"
author: francesco.zubani

categories: [ Data Science, Machine Learning, Enmbeddings, Artificial Intelligence, RAG]
image: assets/images//comparing_embedders_for_rag/cover.png
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

### Can we trust LLMs?

When it comes to AI, discerning between hype and reality can sometimes be challenging.

Modern *large language models* (LLM), indeed, fall within the metaphor of **stochastic parrots**.

(***On the Dangers of Stochastic Parrots: Can Language Models Be Too Big?***, by *Emily M. Bender*, *Timnit Gebru*, *Angelina McMillan*, *Shmargaret Shmitchell*)

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/parrot.png">
    <br>
    <em> AI as "stochastic parrot" (Image by author) </em>
</center>

In short, they are unaware of the language they process, so we cannot fully trust the answers they provide.

So, the answer could be "it depends on our expectations and objectives".

### RAG: "This is the Way"

But what if we wanted to use AI for language-related tasks. ***in a production environment***?

We could follow different strategies reduce hallucinations, such as:
- **Training** a model on a specific dataset
- **Fine-tuning** a pretrained model
- Using **Retrieval Augmented Generation (RAG)**

To achieve our professional goals, RAG stands out as the most suitable option, as it:

1. Avoids <ins>the costs of training/fine-tuning</ins> modeks
2. Allows for **easier updates of sources** as needed

The idea is to provide an enriched context to the LLM, and then use it only for language synthesis.

In this sense, selecting an effective embedder plays a crucial role in the retrieval process.

In fact, the generation of embeddings is the basis of the process of retrieval.

**This article** steps into a possible approach to **evaluate various embedders** for a specific language.

#### STEP 1: Creating samples

I created samples with the following structure:

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

After conducting some research, I compiled a list of effective embedders for Italian:

- [Anita](https://huggingface.co/DeepMount00/Anita)
- [cross_encoder_italian_bert_stsb](https://huggingface.co/nickprock/cross-encoder-italian-bert-stsb)
- [mmarco_bert_base_italian_uncased](https://huggingface.co/nickprock/mmarco-bert-base-italian-uncased)
- [multi_e5](https://huggingface.co/intfloat/multilingual-e5-large)
- [other models ...]

Subsequently, I used ***transformer*** library to download and tokenize models, iterating over my list.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/download.gif">
    <br>
    <em> Downloading and instantiating embedders (Screencast by author) </em>
</center>
<br>

#### STEP 3: Creating the embeddings

I created embeddings for every topic/sentence, iterating over samples in samples' directory.

To achieve this, I used ***langchain_huggingface*** library:

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
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/embeddings.gif" width="1100">
    <br>
    <em> Generating embeddings (Screencast by author) </em>
</center>

#### STEP 4: Storing to Qdrant

Next, I stored these embeddings in [**Qdrant**](https://qdrant.tech/documentation/overview/) using its Python client.

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
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/qdrant.gif" width="1100">
    <em> Creating collections and upserting points (Screencast by author) </em>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/collections.gif" width="1100">
    <em> Qdrant points visualization (Screencast by author) </em>
    <br>
</center>

#### STEP 5: Calculating distances and evaluating results

But how does evaluation process work?

1. **Querying Qdrant**
    * I fetch stored points from a specified collection using a method that scrolls through all items:

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
    * For each topic, I compute the distance between the sentence and the topic embeddings using various metrics:

    ```python
        from scipy.spatial.distance import cosine

        def _compute_distance(vector1, vector2, metric: str):
            if metric == "cosine":
                return cosine(vector1, vector2)
            # Additional metrics can be added here...

        def _evaluate_distances(self, points, categories):
            distances = {category: [] for category in categories}
            for point in points:
                sentence_vector = list(point.vector.values())[0]
                for category, category_vector in categories.items():
                    distance = self._compute_distance(category_vector, sentence_vector, "cosine")
                    distances[category].append({'sentence': point.payload["sentence"], 'distance': distance})

            # Sort distances for each category
            for category in distances:
                distances[category].sort(key=lambda x: x['distance'])
            return distances
    ```

3. **Calculating Averages**
    * I calculate the average distances and standard deviations for each category:

    ```python
    def _calculate_statistics(self, distances):
        category_averages = {}
        for category, values in distances.items():
            distances_list = [item['distance'] for item in values]
            avg_distance = sum(distances_list) / len(distances_list) if distances_list else 0
            std_dev = round(np.std(distances_list), 3) if len(distances_list) > 1 else 0
            category_averages[category] = {"average_distance": round(avg_distance, 3), "std_dev": std_dev}
        return category_averages
    ```

    * By iterating over the distance metrics and models, I conduct evaluations for each combination and store the sorted results accordingly.

#### STEP 6: Generating plots

This final step may be useful for visualizing the results of our evaluations.

Using **matplotlib** and **seaborn**, I implemented a class to generate heatmaps and barcharts from the evaluation results.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/plots.gif">
    <br>
    <em> Generating plots (Screencast by author) </em>
</center>

These visualizations help in understanding the performance of different embedders at a glance.

Additionally, I built a small GUI using the [**streamlit**](https://streamlit.io/) framework, to facilitate interaction with the visual data.

Here’s the final result:

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/viz.gif">
    <br>
    <em> Interactive visualization (Screencast by author) </em>
</center>

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/heatmap.png">
    <br>
    <em> Example heatmap (Image by author) </em>
</center>

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/barchart.png">
    <br>
    <em> Example barchart (Image by author) </em>
</center>

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/comparing_embedders_for_rag/average.png">
    <br>
    <em> Example average and standard deviation (Image by author) </em>
</center>