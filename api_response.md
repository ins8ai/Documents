
# API Response (WIP)

In the below, we review the api response formats provided by various vendors and share the response format ins8.ai will be adopting in version 1.1.0.

## API response formats of other STT/ASR vendors

As of 20230522, below are sample responses. 

### Vendor D

```

```
(description goes here)

### Vendor G

```

```
(description goes here)

### Vendor S

```

```
(description goes here)

### Vendor N

```

```
(description goes here)

## ins8.ai API response format

As of 20230522, refactoring and performance enhancement work is being worked on.
Team is in the midst of transitioning from its existing api response format to the new one outlined below.

### Current (version X.X.X)

```

```

### Planned Release (version X.X.X)

JSON object returned is structured with the following considerations:
  - metadata section
    - contains general information such as filename, transcription datatime, audio duration, etc
  - transcription section are organised in a hierachy in the following order
    1. Channel
      - transcription text of the entire duration of the audio clip uploaded
    2. Sentence
      - transcription text of each sentence detected
      - timestamp, start and end time
      - sentence level confidence score
    3. Word
      - transcription text of the word
      - timestamp, start and end time
      - word level confidence score
      - speaker tag

If there are two channels, the entire hierachy of channel > sentence > word level outputs are repeated for the different channels transcribed.
Below is a sample response with in the new API endpoint.

```


```

