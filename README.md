# Jellyfin Prediction

Predicting movies to watch from a Jellyfin library based on previously watched and favorited movies 

## Goal 

Utilize the watched movie list as a training/eval dataset with IsFavorite being the target feature. By default Jellyfin provides: 

- PremiereDate
- CriticRating
- OfficialRating
- Overview
- Taglines
- Genres

And more, which can be used as features for training. 

## Procurement

Data can be loaded from our Jellyfin library, using the Emby Swagger documentation to understand definitions. From there, a query can be built to only take watched movies - adding additional features such as studio and actors to enrich the data. 

## Processing 

Due to the wide variety of data, many types of processing will have to be utilized. 

- Binary values such as IsFavorite, can simply stay as PD bools
- Date released can be converted to a PD date and then many individual features such as the year, month, weekend, etc. 
- Title, tagline, and overview can be converted with TF-IDF to a frequency vector for the model to understand 
- Genre, studio, and rating can be converted to one-hot encoding
- Actors/People can be converted into a count as well as keeping individual people encoded in one hot
- And the rest can be maintained as numerical normalized

For the model to understand this, it's also converted into a sparse matrix 

## Training 

Using a standard 80/20 split, a Random Forest model will train on the dataset since it provides a good balance of accuracy and speed. 

To ensure the best hyperparams, a grid search will try all relevant parameters. 

From there, the goal is an F1 of above 0.8, ensuring the model can make relevant and accurate predictions. 