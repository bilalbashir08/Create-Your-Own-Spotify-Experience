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

    >- User-Defined Function for MFCC Extraction
        extract_mfcc_udf = udf(extract_mfcc, ArrayType(FloatType()))
This line defines a Spark user-defined function (UDF) that wraps the previously defined extract_mfcc function, which computes the MFCCs from an audio file. The UDF is registered to return an array of floats (ArrayType(FloatType())), which represents the MFCC features.

