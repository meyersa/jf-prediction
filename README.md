# Jellyfin Prediction

Predicting movies to watch from a Jellyfin library based on previously watched and favorited movies using a Random Forest model and FastText for vectoring text information. 

[Read more about it here](https://meyersa.com/projects/jf-prediction)

# Results

| Evaluation Metric | Value  | 
| ----------------- | -----  |
| **Accuracy**      | 0.6944 |
| **Precision**     | 0.6375 |
| **Recall**        | 0.9273 | 
| **F1**            | 0.7556 | 
| **AUC**           | 0.7966 | 

| Confusion Matrix | Predicted False | Predicted True |
|----------------- |-----------------|----------------|
| **Actual False** |               24| 29             |
| **Actual True**  | 4               | 51             |

# Using

Once the ENVs have been filled out, simply run the notebook. 

- The top codeblock will install the necessary pip packages from the `requirements.txt` file which includes the FastText-wheel package - I had to use this since the FastText normal package would not build on Windows - this might have to be changed around. 

- FastText will also install the models itself using their Utility later in the pipeline. 

For a library with about 3,000 movies, it took around 10 minutes to fit everything. Training took about 30 seconds on a 9900K.

## ENVs 

To use this project, first create a `.env` file in the same directory as the notebook. Fill in the following values 

| Name    | Value            |
| ----    | -----            |
| URL     | Jellyfin Server  |
| USER_ID | Jellyfin User ID |
| API_KEY | Jellyfin API Key |

## Requirements 

- No GPU since FastText is CPU based
- Lots of RAM (32gb) since FastText loads into memory - the more movies the more memory usage




# Future Plans 

Ideally in the future I'll expand the Jellyfin client portion of this to also support adding back the recommendations as some kind of library on Jellyfin. 

This has not happened yet as there does not seem to be an optimal way to do this, for example it can't show up like Collections or Movies without making media entries on the disk and playlists are too out of the way. This is pending new library types for Jellyfin or a better understanding of how the server works. 