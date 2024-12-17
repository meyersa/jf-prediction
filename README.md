# Jellyfin Prediction

Predicting movies to watch from a Jellyfin library based on previously watched and favorited movies 

In this case, the watched movie list is used as a training/eval dataset with IsFavorited being the key label 

From there, many other features from Jellyfin about the movie can be used to predict, such as 

- PremiereDate
- CriticRating
- OfficialRating
- Overview
- Taglines
- Genres
- Etc. 

