# Jellyfin Prediction

Predicting movies to watch from a Jellyfin library based on previously watched and favorited movies using a Random Forest model and FastText for vectoring text information. 

## Goal 

Utilize the watched movie list as a training/eval dataset with IsFavorite being the target feature. By default Jellyfin provides: 

- PremiereDate
- CriticRating
- OfficialRating
- Overview
- Genres

And more, which can be used as features for training. 

## Procurement

Data can be loaded from our Jellyfin library, using the [Emby Swagger](https://swagger.emby.media/?staticview=true) documentation to understand definitions. From there, a query can be built to only take watched movies - adding additional features such as studio and actors to enrich the data. 

## Processing 

Due to the wide variety of data, many types of processing will have to be utilized. 

- Binary values such as IsFavorite, can simply stay as PD bools
- Date released can be converted to a PD date and then many individual features such as the year, month, weekend, etc. 
- Title, overview, people, and studios can be converted into a FastText vector
- Genre and rating can be converted to one-hot encoding
- Actors/People can also be converted into a count
- And the rest can be maintained as numerical normalized

For the model to understand this, it's also converted into a sparse matrix 

## Training 

Using a standard 80/20 split, a Random Forest model will train on the dataset since it provides a good balance of accuracy and speed. 

To ensure the best hyperparams, a grid search will try all relevant parameters. 

## Results

| Evaluation Metric | Value  | 
| ----------------- | -----  |
| **Accuracy**      | 0.6204 |
| **Precision**     | 0.6    |
| **Recall**        | 0.6792 | 
| **F1**            | 0.6372 | 
| **AUC**           | 0.6758 | 

| Confusion Matrix | Predicted False | Predicted True |
|----------------- |-----------------|----------------|
| **Actual False** | 31              | 24             |
| **Actual True**  | 17              | 36             |

At first the results appear to be good, considering an F1 of 0.63, however that doesn't seem as good as it actually is when the accuracy is only 0.62. This means that the model can make guesses - but they are really just guesses, it only has a slight advantage over just randomly 50/50ing. Looking at the results produced by the model, they do not really make sense as recommendations - the top recommendation being a 20th century black and white silent movie that was released 2 years ago. Even that movie was only a 0.74 match, which is not as confident as I'd have hoped. On the opposite spectrum, the worst recommendation was a 0.34 - still not confident. 

Because of the lackluster performance, I would not truly consider this a success. But I don't think the model is completely to blame for the performance, instead I attribute it to the data. Random vectors don't exactly provide a good patterning system for the model, resulting in random guesses like it produced. Instead, running it simply without the IDF parts seems to do better. 
