Garin Jankowski

# Link Prediction on a Food Web of Kelp Forests of California

## Abstract

Food webs are a representation of ecosystem data that models the relationships of consumption and predation between organisms. Many kinds of tasks can be applied to food webs, such as food web generation, prediction of organism features, and prediction of relationships between organisms. The goal of this project was to take a food web graph of some California kelp forests and perform link prediction, attempting to learn the food and consumption-based relationships between organisms and predicting the existence of relationships that have not been recorded. Using a GraphSAGE based encoder and a simple multi-layer perceptron classifier, an accuracy of 88.16% was reached. This is less than ideal, and the higher recall of 92.58% suggests a greater ability to predict that a relationship exists rather than predicting if one doesn’t.

## Introduction

A food web is a model that describes the organisms in an ecosystem and the food chains they are a part of. Food webs are very useful in describing relationships between species of organisms and the energy transfer between them. Depending on the detail and data collected, a food web can show type of predation/consumption, what season these occur in, and what stage of life these organisms are at during these interactions. This helps in keeping track of an ecosystem and its stability.

Food webs are a natural choice for graph-based approaches because it is already a graph. Each node is an organism, with features varying from taxonomy to habitat. Each edge connects one organism to another based on who eats whom, making this a directed graph. Different edge features can be considered, like type of predation and frequency of predation within a certain sample.

Many types of graph machine learning can be applied to this. It seems that most food web related work involving machine learning focuses on generating food webs, which is a graph generation problem. One could also find node level classification problems by trying to predict the taxonomy of a node based on its relationships in the food web. This project focuses on edge-level prediction, which means trying to figure out the likelihood of a relationship between two organisms. This could be potentially useful for predicting the existence of relationships that have not been recorded and predict relationships for new organism discoveries being entered into the food web.

## Data

The dataset is an already constructed food web of some of the kelp forest areas in the Santa Barbara Channel in California. There are 1098 nodes and 21,956 edges recorded between these nodes. The nodes, kept in one file, consist of numerous features, 45 in total. Notable features include stage of life, symbiont vs. free living, geographical range, and taxonomic rankings like order, family, genus, and species. Edges, kept in another file, consist of the node ID and name of the consumer, the node ID and name of the resource, and the type of consumer interaction like predation or parasitoid. There are also some other edge features like confidence and a justification/source of data for the relationship.

There are several other files included in this dataset, most of which are specifics regarding certain types of organisms, like location and behavior data on zooplankton and small gastropods. Lastly, there is a file Column_Descriptors.csv that describes all the features of each file. These extra files were not considered in the experiment. Below are species counts of the recorded organisms and whether they are free living or parasitic.

## Methods	                          

The first step was feature selection. The data used was only from the node and edge files. Out of the 45 node features, 12 were used: staged, Stage, Phylum, Class, Order, Family, Genus, Habitat_Site, Consumer.Strategy, Consumer.Type, Mobility, and Life.Cycle. These features were all converted to categorical type and one-hot encoded, creating 1502 features. The same was done for the edges. The edge features used were Consumer.Interaction.Code and Feeding.Site. These were one-hot encoded to create 13 edge features total, although these did not end up being used in the final model.

Two networks were used. The first was a GNN that was used to learn node embeddings. This network follows the GraphSAGE framework, so that a low dimensional feature space can be learned from the numerous amount of features. The architecture consists of three SAGEConv: 1502x64, 64x32, and 32x32, with ReLUs following each one. This is finished off by a 32x8 fully connected layer, bringing the final amount of feature dimensions to 8.

The second network was a simple feed forward network for classifying whether an edge exists between two nodes. This consists of three fully connected layers: 8x50, 50x10, 10x1, with ReLU layers in between and a sigmoid applied to the final value. This returns a probability of a relationship existing or not.

The training/validation/testing was split 70/10/20. For each batch, positive samples, which were edges that exist in the graph, were evaluated and trained on. Next, the same amount of negative samples was created by generating edges that did not exist in the graph. The positive and negative predictions were recorded separately, with the loss being the negative mean log of the positive predictions minus the mean log of one minus the negative predictions.

$$ L= -mean(log(y_p)) - mean(log(1 - y_n))$$

## Results

The model performed okay at predicting the existence of an edge between two nodes. With the higher recall, it seemed better at predicting that an edge exists than predicting that one doesn’t. There is a potential domain reason for this, which is that the supplied graph data is by no means perfect and that not every single relationship has been observed and recorded. This could even mean that the model is predicting relationships that do exist but were not recorded in the graph, which is the primary application of this model. However, one cannot be sure without perfect data, which we do not have.

## Conclusions

The trained GraphSAGE model and MLP classifier showed almost decent performance in predicting whether or not a relationship exists between two organisms in the kelp forest food web. It is a possibility that the model is discovering information not yet gathered by humans and correctly predicting a few unrecorded relationships between nodes. Future work would involve further improving the architecture of both the encoder and the classifier models and also predicting the direction and type of consumption that occurs between the organisms.


## References

Morton, D. et al., A food web including parasites for kelp forests of the Santa Barbara Channel, California, https://datadryad.org/stash/dataset/doi:10.25349/D9JG70

Jain, T., Online Link Prediction with Graph Neural Networks, https://medium.com/stanford-cs224w/online-link-prediction-with-graph-neural-networks-46c1054f2aa4

pytorch-geometric Colab Notebooks and Video Tutorials, https://pytorch-geometric.readthedocs.io/en/latest/get_started/colabs.html
