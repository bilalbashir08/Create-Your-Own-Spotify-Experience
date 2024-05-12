22I-1902 MOAZ MURTAZA

22I-1901 BILAL BASHIR

21I-1775 ALIZEH QAMAR 


# CREATE-YOUR-OWN-SPOTIFY-EXPERIENCE

This innovative project involves developing a sophisticated music streaming service that not only offers playback functionalities but also features a dynamic music recommendation system. By leveraging real-time user activity data, this platform will deliver personalized music suggestions, ensuring a tailored and engaging listening experience for every user.

##### This README outlines the comprehensive architecture and features of the Music Streamer project : 

Designed as a streamlined alternative to conventional music streaming services. The project encompasses the development of a sophisticated music recommendation system, integrated with playback and streaming capabilities, and enhanced by real-time user activity analysis.

 - _Dataset:_
Description of the music metadata dataset used, including details about song tracks, artist information, user activity logs, and listening histories.

 - _Pre-processing:_
Explanation of the methods employed to clean, format, and prepare the dataset for efficient use in real-time streaming and recommendation generation.

 - _Streaming Pipeline:_
Architectural design of the streaming pipeline featuring Apache Kafka. This section includes descriptions of the producer application responsible for ingesting music playback data and the consumer applications that process this data for recommendations.

 -  _Recommendation Algorithms:_
Implementation details of machine learning algorithms used to power the music recommendation engine. This includes collaborative filtering, content-based filtering, and other relevant techniques tailored to enhance user experience through personalized suggestions.

 - _Database Integration:_
Integration strategies with a NoSQL database (recommended: MongoDB) for storing user profiles, music metadata, and recommendation outputs to facilitate efficient data retrieval and scalability.

 - _ Playback and Streaming:_
Design and development of the playback and streaming module that allows users to listen to music, manage playlists, and interact with the service in real-time.

 -  _User Interface:_
Overview of the user interface design, focusing on usability, aesthetic appeal, and functionality that supports easy navigation and interaction with the music content.

 -  _Bonus: Deployment and Automation Scripts (Optional):_
Provision of optional bash scripts for automating the deployment of the system components and managing the orchestration of the streaming services.

This project aims to demonstrate the practical application of modern streaming and recommendation technologies to create a user-centric, efficient, and responsive music streaming service.


### PHASE 1 : PREPRCESSING , TRANSFORM , LOAD (ETL) PIPELINE

This Python code snippet is designed to work with the PySpark framework and the librosa library to extract MFCC (Mel Frequency Cepstral Coefficients) features from audio files. Here's a step-by-step explanation of each part of the code:

##### Importing Libraries and Modules:

-  _from pyspark.sql import SparkSession:_ Imports the SparkSession module, which is the entry point to programming Spark with the DataFrame API.
  
- _from pyspark.sql.functions import udf:_ Imports the udf (user-defined function) module, which allows you to create Python functions and use them in Spark SQL queries.
  
- _from pyspark.sql.types import StringType, ArrayType, FloatType:_ Imports data types used in defining schemas for Spark DataFrames.

- _import librosa_: Imports the librosa library, which is used for audio and music analysis, particularly useful here for extracting audio features.

-_ import os:_ Imports the os library, typically used for interacting with the operating system.

- i_mport gc:_ Imports the gc module, which is used for manual garbage collection.

- _import audioread:_ Imports audioread, which can decode audio files using multiple backends.

- _from audioread.exceptions import NoBackendError:_ Imports the specific exception to handle cases where no suitable backend is available to decode an audio file.
 
- _Defining the Function extract_mfcc:_
This function takes a single parameter, audio_path, which is the path to the audio file from which features are to be extracted.

- _The try block attempts to execute the librosa-based feature extraction:_
audio, sample_rate = librosa.load(audio_path, sr=None): This line loads the audio file. Setting sr=None allows the audio to be loaded at its original sample rate.

- _mfcc_features = librosa.feature.mfcc(y=audio, sr=sample_rate, n_mfcc=100).mean(axis=1)_: Extracts 100 MFCC features from the audio data. librosa.feature.mfcc computes the MFCCs of the audio signal. The .mean(axis=1) computes the average of the MFCC over time, reducing the result from a 2D array to a 1D array of mean MFCC values.

- _return mfcc_features.tolist():_ Converts the MFCC features from a NumPy array to a list and returns this list.

- _The except NoBackendError as e:_ This block catches exceptions raised when no suitable backend is found for decoding the audio file, printing an error message.

This code is typically used in audio processing tasks where features need to be extracted from sound files for analysis, classification, or other forms of machine learning tasks involving audio data.

  ##### User-Defined Function for MFCC Extraction
        extract_mfcc_udf = udf(extract_mfcc, ArrayType(FloatType()))
        
This line defines a Spark user-defined function (UDF) that wraps the previously defined extract_mfcc function, which computes the MFCCs from an audio file. The UDF is registered to return an array of floats (ArrayType(FloatType())), which represents the MFCC features.

##### Processing Audio Files and Saving Results to MongoDB
python

    def process_and_save_to_mongo(file_list, spark_session):
    # Create DataFrame from list of audio file paths
    df_audio_paths = spark_session.createDataFrame(file_list, StringType()).toDF("audio_path")
    
    # Apply the MFCC extraction UDF and repartition DataFrame for parallel processing
    df_mfcc_features = df_audio_paths.repartition(100).withColumn("mfcc_features", extract_mfcc_udf(df_audio_paths["audio_path"]))
    
    # Show the DataFrame for verification
    df_mfcc_features.show(truncate=False)
    
    # Save the DataFrame to MongoDB
    df_mfcc_features.write.format("com.mongodb.spark.sql.DefaultSource").mode("append").save()
    
    # Clean up DataFrames to free memory
    del df_audio_paths, df_mfcc_features
    gc.collect()
    
This function takes a list of audio file paths and a Spark session as arguments. It performs the following operations:

- Creates a DataFrame from the list of audio file paths.
- Repartitions the DataFrame into 100 partitions for better parallel processing performance.
- Applies the MFCC extraction UDF to each row in the DataFrame.
- Displays the resulting DataFrame containing paths and their corresponding MFCC features.
- Saves the DataFrame to MongoDB using the MongoDB Spark Connector.
- Cleans up memory by deleting DataFrames and invoking garbage collection.




##### Spark Session Setup

    spark = SparkSession.builder \
      .appName("AudioFeaturesExtraction") \
      .config("spark.mongodb.input.uri", input_uri) \
      .config("spark.mongodb.output.uri", output_uri) \
      .config("spark.jars.packages", "org.mongodb.spark:mongo-spark-connector_2.12:3.0.2") \
      .getOrCreate()
      
This section sets up a Spark session configured for MongoDB integration and application-specific settings. It specifies:

- Application name.
- MongoDB input and output URIs.
- The MongoDB Spark Connector library needed for integration


##### File Discovery and Listing

    folder_path = "DataSet"
    all_folders = [os.path.join(folder_path, file_name) for file_name in os.listdir(folder_path)]
    audio_files = []
    for folder in all_folders:
       for file_name in os.listdir(folder):
           if file_name.endswith('.mp3'):
              audio_files.append(os.path.join(folder, file_name))
              
This part lists all MP3 files in a specified dataset folder and its subfolders:

- Constructs a list of folder paths within the main dataset folder.
- Iterates through each folder and subfolder to find and list all MP3 files.
  
This is a comprehensive way to process and analyze audio data within a Spark environment, illustrating the integration between data processing, feature extraction, and database operations.


### PHASE 2 : MUSIC RECOMMENDATION MODEL (MODEL TRAINING)

##### Function Definitions for Audio Processing

> - Function extract_mfcc:

> - Purpose: Extracts MFCC (Mel-frequency cepstral coefficients) features from an audio file using the librosa library. MFCCs are commonly used in audio processing tasks such as speech and music analysis.
> - Parameters: audio_path (path to the audio file).
> - Returns: A list of mean MFCC features for the audio file.
> - Error Handling: Catches NoBackendError if no suitable audio backend is found to process the file.

##### Function load_data_from_mongodb:

> - Purpose: Loads data from MongoDB using Spark's MongoDB connector.
> - Parameters: spark_session (the active Spark session), input_uri (MongoDB connection URI).
> - Returns: A DataFrame loaded from MongoDB.

##### Function create_feature_vector:

> - Purpose: Converts an array of features into a Spark MLlib Vector object.
> - Operations: Uses a UDF (user-defined function) to transform the 'mfcc_features' column into a 'features' column that contains dense vectors.

##### Function normalize_features:

> - Purpose: Normalizes feature vectors to unit norm using Spark MLlib's Normalizer.
> - Operations: Transforms the 'features' column into 'normalized_features' column.

 #### AnnoyIndex for Recommendation System

##### Function train_recommendation_model:

> - Purpose: Trains a recommendation model using the Annoy library to build an approximate nearest neighbor index.
> - Parameters: df (DataFrame with feature vectors), num_trees (number of trees for building the Annoy index).
> - Operations: Adds feature vectors to the Annoy index and builds the index.

##### Function load_trained_annoy_index:

> - Purpose: Loads a trained Annoy index from file.
> - Returns: An Annoy index object.

#### Data Handling and Spark Session Setup

##### Initialization of Spark Session:

> - Operations: Configures and initializes a Spark session with MongoDB connectivity and handles package dependencies.

##### Function process_audio_file:

> - Purpose: Processes an audio file to extract features, create a feature vector, and normalize it.
> -Operations: Uses previously defined functions to create a DataFrame for a single audio file and preprocess it.

##### Loading and Displaying Data from MongoDB:

> - Operations: Loads data into a DataFrame using load_data_from_mongodb, transforms it using create_feature_vector, and displays the initial few rows.
> - General Workflow

The script first defines functions for extracting features, loading data, and training models.

A Spark session is set up with specific configurations for MongoDB.
Data is loaded from MongoDB, processed, and used to train a recommendation model using Annoy for fast retrieval of similar items.
This script is a robust example of integrating audio processing, Spark data handling, and machine learning for building a scalable recommendation system.

##### MUSIC RECOMMENDATION.ANN 

1.**Loading the Index:** The function load_trained_annoy_index() loads this .ann file into an Annoy index object. This loaded index can then be used to query for nearest neighbors.
2.**Querying for Neighbors:** When a new song is processed, its features are extracted and then used to query this index to find other songs in your dataset that have the closest matching features (e.g., similar tempo, rhythm, or overall sound texture as determined by MFCC).

This method of using an Annoy index is particularly useful in applications like music recommendation systems, where you need to quickly find items (songs) that are similar to each other based on their audio characteristics.

### PHASE 3 :  DEPLOYMENT 

#### FRONTEND AND BACKEND (USING HTML , CSS AND FLASK)

##### HTML - CSS 

1. **Document Structure Overview**

- Doctype and HTML Tag: <!DOCTYPE html> specifies the document type and version, and <html lang="en"> starts the HTML document with English as the primary language.
- Head Section: Contains metadata, links to stylesheets, scripts, and other resources needed for the document.
- Body Section: Comprises the visual content of the page, including navigation, music list, playback controls, and additional interactive elements.
  
2.**Detailed Breakdown**

 _Head Section_
    - Character Set and Compatibility: <meta charset="UTF-8" /> sets the character encoding to UTF-8, which includes most characters from all known human 
      languages. <meta http-equiv="X-UA-Compatible" content="IE=edge" /> ensures the page uses the latest rendering mode in Internet Explorer.
    - Viewport: <meta name="viewport" content="width=device-width, initial-scale=1.0" /> makes your page responsive by setting the viewport width to the device 
      width and initial zoom level to 1.
 _Title:_ <title> defines the title of the webpage, which appears in the browser tab.
     - Stylesheets and Icons: Links to an external CSS file for styling and a favicon encoded in Base64 format.
     - JavaScript: Links to an external JavaScript file for interactivity and includes a script for Font Awesome icons, enhancing the UI with scalable icons.
  _Body Section_
Navigation (nav) and List (ul): Contains a single list item (li) with the brand logo and name, serving as a simple navigation bar.

3.**Main Content (div.container):**

> - Song List: A heading introduces the section followed by multiple song items. Each song item includes a placeholder for an image, song name, and a play button with a unique ID. The timestamp span shows the song duration.
> -Banner: An empty div that could be used for displaying currently playing song artwork or advertisements.

4.**Playback Controls (div.bottom):**

> - Progress Bar: An input of type range, used as a progress bar to show and control the current position of the song.
> - Control Icons: Previous, play/pause, and next buttons to control song playback, leveraging Font Awesome for the icons.
> - Current Song Display: Shows an animated GIF (acting as a visualizer) and displays the name of the currently playing song.
>- Copyright and Link: Provides a copyright notice and a link to the original project or template used as a reference or base.
>- External Resources
> - Font Awesome: Used for icons throughout the UI, enhancing the visual appeal and providing intuitive control icons for the music player.

**Summary**

This HTML layout is designed for a basic music streaming service, providing a list of songs, basic navigation, and playback controls. The use of external scripts and stylesheets simplifies the management of the page's look and behavior, allowing for a cleaner HTML structure. The page is responsive and accessible, adhering to modern web standards.


##### FLASK WITH KAFKA & PYSPARK

1.**Flask App Setup**

> - Imports: The script imports necessary libraries from Flask, Kafka, PySpark, librosa for audio processing, and Annoy for approximate nearest neighbors algorithm.
> - Flask Application Initialization: An instance of the Flask class is created. This instance is used to handle requests and responses on your web server.

2.**Apache Kafka Configuration**

> -  **Kafka Producer and Consumer Setup:** The Kafka Producer and Consumer are configured with the necessary settings (e.g., broker URL and group ID). These are used to send and receive messages (audio paths and processed data) between different parts of your application or different services.

3. **PySpark Configuration**

> - Spark Session: A Spark session is initialized to allow for distributed data processing using PySpark. It connects to MongoDB to load audio feature data.
> - Data Loading and Processing Functions: These include functions to load audio data, extract Mel-frequency cepstral coefficients (MFCCs), convert arrays to Spark vectors, and normalize features for machine learning processing.

4.**Audio Processing**

> - extract_mfcc: This function loads an audio file using librosa and extracts its MFCC features, which are commonly used in speech and audio processing.
process_audio_file: Takes an audio path, processes it to extract features, converts those features to a format suitable for Spark operations, and normalizes them.

5. **Data Handling with MongoDB**
   
> - load_data_from_mongodb: Connects to MongoDB and loads data into a DataFrame for processing.
> - Vector and Normalization Functions: Include utility functions to convert lists to vectors and to normalize feature vectors in the DataFrame.

6. **Flask Routes**
   
> - Static File Serving: Routes to serve MP3 files and cover images from directories.
> - Index Page: Serves the main HTML file.
> - Process Audio: Takes an audio path from a POST request, sends it to Kafka, and acknowledges the request.
> - Get Neighbors: Handles POST requests to retrieve nearest neighbors for a given audio file. It processes the audio file, loads a trained Annoy index, finds 
    the nearest neighbors, and returns the paths of these neighbors formatted in HTML.

**Execution**

> - If run directly, the script starts the Flask application, making it accessible via a web browser. This is helpful for local development or testing.

**Application Flow**

> - User Interaction: A user interacts with the web application through the frontend (HTML/CSS/JS), which is not fully shown in this script but is referenced by the Flask routes.
> - Audio Processing: When a user uploads or selects an audio file for processing, the frontend sends a request to the Flask server, which then handles audio feature extraction, and sends the audio path to Kafka.
> - Recommendation: After processing, the application uses the Annoy index to find similar songs based on the audio features and returns these as recommendations to the user.

This application is the integration of  Flask with data processing libraries like PySpark and Kafka for real-time data streaming and processing tasks, specifically tailored for a music recommendation system.



## CONTRIBUTORS 

> [moaz-murtaza](https://github.com/moaz-murtaza)(i221902@nu.edu.pk)

> [ bilalbashir08](https://github.com/bilalbashir08) (i221901@nu.edu.pk)

> [Alizeh21](https://github.com/Alizeh21)(i211775@nu.edu.pk)










