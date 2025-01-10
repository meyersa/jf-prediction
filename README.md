# Jellyfin Prediction

Predicting movies to watch from a Jellyfin library based on previously watched and favorited movies using a Random Forest model and FastText for vectoring text information. 

## Goal 

Utilize the watched movie list as a training/eval dataset with IsFavorite being the target feature. By default Jellyfin provides: 

- PremiereDate
- CriticRating
- OfficialRating
- Overview
- TagLines
- Genres
- CommunityRating
- ProductionYear
- People
- Studios

And collections can be added for more context: 

- CollectionName 
- CollectionSize
- CollectionAge
- CollectionOrder
- FirstInCollection
- AgeSinceLastMovie

With this information unwatched movies can then be scored for the likely hood that they could be a "favorite" and thus recommended. 

## Procurement

Data can be loaded from our Jellyfin library, using the [Emby Swagger](https://swagger.emby.media/?staticview=true) documentation to understand definitions. From there, a query can be built to only take watched movies - adding additional features such as studio and actors to enrich the data. 

## Processing 

Due to the wide variety of data, many types of processing will have to be utilized. 

(FastText, Date, Scalar, Onehot, MLB, Ordinal, Binary)

- FastText: Text vectoring for values such as the Title, Overview, and Tagline
- Date abstraction: Taking the premiere data and engineering weekend, month, year, etc.
- Scalar: Numerical data such as the community rating, collection size, etc. 
- Onehot: Values consistent across multiple entries such as community rating
- MultiLabelBinarizer: Values sort of consistent but in lists such as actors, genres, etc. 
- Ordinal: Ranking of movies in collections
- Binary: Favorite and engineered features like first in collection

For the model to understand this, it's also converted into a sparse matrix, which has the unfortunate property of removing column names making it hard to understand. 

After MLB and Onehot, there are far too many features to be usable, so this can be reduced with TruncatedSVD. 

> This greatly improved F1

## Training 

Using a standard 80/20 split, a Random Forest model will train on the dataset since it provides a good balance of accuracy and speed. 

To ensure the best hyperparams, a grid search will try all relevant parameters. 

On a 9900K 100 fits took about 30 seconds with TruncatedSVD, without it took about 2 minutes. 

## Results

| Evaluation Metric | Value  | 
| ----------------- | -----  |
| **Accuracy**      | 0.6944 |
| **Precision**     | 0.6375 |
| **Recall**        | 0.9273 | 
| **F1**            | 0.7556 | 
| **AUC**           | 0.7966 | 

| Confusion Matrix | Predicted False | Predicted True |
|----------------- |-----------------|----------------|
| **Actual False** | 24              | 29             |
| **Actual True**  | 4               | 51             |

For guessing movie recommendations with little to no consistency (I don't even know why I like the movies I like) this ended up being impressive. An F1 of 0.76 means that it isn't quite guessing, it's actually making real decisions. An accuracy of 0.69 means that it also is decent at making those decisions. Looking at the Confusion Matrix, it does very well at predicting the true positives, but suffers from false positives. 

Early iterations before I had included collection (Movies Series) information suffered much worse in this department. Overall it seems with the added information it predicts pretty well. 

I am a little disappointed though as I was hoping this would have achieved an F1 of 0.8 and it felt like it was so close to crossing that bridge for a while. In the end I do really think it just comes down to this being random guesses so it even boggles my mind a little that it can form these predictions. 

Aligning with my suspicions are the real recommendations, which only score as high as 0.67 and as low as 0.37 meaning that it really has no strong ideas. 

## Discussion 

With FastText for text vectoring the model is a little sluggish on preprocessing, but the actual predictions are fast. This makes me believe it could be integrated into Jellyfin as a plugin that occasionally runs to build recommendations - not like the "You watched this, try this" recommendations, but because you've watched everything you have, this different thing could be cool - something I would find very useful. And since most Jellyfin servers are already pretty beefy, this could easily run in the background. 