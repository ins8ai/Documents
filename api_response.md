
# API Response (WIP)

In the below, we review the api response formats provided by various vendors and share the response format ins8.ai will be adopting in version 1.1.0.

## API response formats of other STT/ASR vendors

As of 20230522, below are sample responses from different stt service providers.

### Vendor D

```
{
  "metadata": {
    "transaction_key": "deprecated",
    "request_id": "85e7f5ca-d1bf-45f3-812e-0558ffce7394",
    "sha256": "6e97736923bdfef950449e607c099e19372bb946af0348fe4f512f85b9cb93a0",
    "created": "2021-09-24T03:32:46.752Z",
    "duration": 96.405365,
    "channels": "1",
    "models": [
      "41757536-6114-494d-83fd-c2694524d80b"
    ]
  },
  "results": {
    "channels": [
      {
        "alternatives": [
          {
            "transcript": "This is sample transcript.",
            "confidence": 0.9938247,
            "words": [
              {
                "word": "This",
                "start": 9.475,
                "end": 9.715,
                "confidence": 0.9928706
              },
              {
                "word": "is",
                "start": 9.715,
                "end": 9.995,
                "confidence": 0.9911145
              },
            ]
          }
        ]
      }
    ]
  }
}
```
- Depending on the selected features, JSON response will be include/exclude the different key value pairs.
- Response is first made up of a 'metadata' section, followed by a 'results' section that is composed of channels.
- 'channels' section is an array of the different channels detected in the audio file.
- Each element in the channels array is made up of the transcription which contains the full transcript.
- 'words' component alongside the transcript is then an array of the detected words which are timestamped and marked with confidence scores. 

### Vendor G

```

```
(description goes here)

### Vendor S

```
{
  "results":[
    {
      "alternatives" :[{ "confidence" : 1.0, "content" : "Come", "language" : "en", "speaker" : "UU"}],
      "end_time": 2.09,
      "start_time" : 1.85,
      "type" : "word"
    },
  ]
}

```
- Output stays consistent regardless of features selected.
- If feature is not selected, output will return 'null'.
- Results is an array of different dictionaries. Each dictionary has a start/end time and may contain one or more alternatives.
- Speaker tagging is embedded in each alternative and values are either S# e.g. S1, S2 to denote speaker; UU represents unidentified or when diarization is turned off.

## ins8.ai API response format

As of 20230522, refactoring and performance enhancement work is being worked on.
Team is in the midst of transitioning from its existing api response format to the new one outlined below.

### Current (version X.X.X)

```

```

### Planned Release (version X.X.X)

```


```

Above is a sample response with in the new API endpoint.
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


