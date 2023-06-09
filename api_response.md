
# API Response

In the below, we review the api response formats provided by various vendors and share the response format ins8.ai will be adopting in version 2.0.0.

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
- depending on the selected features, JSON response will be include/exclude the different key value pairs
- response is first made up of a 'metadata' section, followed by a 'results' section that is composed of channels
- 'channels' section is an array of the different channels detected in the audio file
- each element in the channels array is made up of the transcription which contains the full transcript
- 'words' component alongside the transcript is then an array of the detected words which are timestamped and marked with confidence scores

### Vendor G

```
{
  "name": "6212202767953098955",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeMetadata",
    "progressPercent": 100,
    "startTime": "2017-07-24T10:21:22.013650Z",
    "lastUpdateTime": "2017-07-24T10:21:45.278630Z"
  },
  "done": true,
  "response": {
    "@type": "type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeResponse",
    "results": [
      {
        "alternatives": [
          {
            "transcript": "Four score and twenty...(etc)...",
            "confidence": 0.97186122,
            "words": [
              {"startTime": "1.300s", "endTime": "1.400s", "word": "Four"},
              {"startTime": "1.400s", "endTime": "1.600s", "word": "score"},
              {"startTime": "1.600s", "endTime": "1.600s", "word": "and"},
              {"startTime": "1.600s", "endTime": "1.900s", "word": "twenty"},
              ...
            ]
          }
        ]
      },
      {
        "alternatives": [
          {
            "transcript": "for score and plenty...(etc)...",
            "confidence": 0.9041967,
            ...
          }
        ]
      }
    ]
  }
}
```
- transcription 'results' is nested within 'response' section alongside other useful 'metadata' and 'status' sections
- 'results' include different transcription alternatives
- each alternative contains sentence level results as well as word level results
- time stamps are organised and availed in the lowest word level results

### Vendor S

```
{
   "format":"2.4",
   "job":{
      "created_at":"2019-01-17T17:50:54.113Z",
      "data_name":"example.wav",
      "duration":275,
      "id":"yjbmf9kqub"
   },
   "metadata":{
      "created_at":"2019-01-17T17:52:26.222Z",
      "transcription_config":{
         "diarization":"none",
         "language":"en"
      },
      "type":"transcription"
   },
   "results":[
      { "alternatives":[{"confidence":0.9, "content":"Just", "language":"en", "speaker":"UU"}], "end_time":1.07, "start_time":0.9, "type":"word" },
      { "alternatives":[{"confidence":1, "content":"this", "language":"en", "speaker":"UU"}], "end_time":1.44, "start_time":1.11, "type":"word" },
      { "alternatives":[{"confidence":1, "content":".", "language":"en", "speaker":"UU"}], "end_time":273.64, "start_time":273.64, "type":"punctuation"}
   ]
}

```
- 'results' is an array of different dictionaries. Each dictionary has a start/end time and contain one or more alternative predictions
- speaker tags are embedded in each alternative and values are either S# e.g. S1, S2 to denote speaker; UU represents unidentified or when diarization is turned off
- start and end timestamps are also embedded in each dictionary

## ins8.ai API response format

As of 20230522, refactoring and performance enhancement work is being worked on.
Team is in the midst of transitioning from its existing api response format to the new one outlined below.

### Current (ver. 1.7.0)

```
{
    "status": "success",
    "transcript": [
        { "start_time": 0.311, "end_time": 0.695, "word": "call one", "channel": 1},
        { "start_time": 1.647, "end_time": 1.687, "word": "call", "channel": 0},
        { "start_time": 1.73, "end_time": 1.949, "word": "banking", "channel": 1},
        { "start_time": 1.972, "end_time": 2.746, "word": "one banking", "channel": 0},
        { "start_time": 5.001, "end_time": 7.724, "word": "hi thank you for calling a a b c bank uh how may i help you today", "channel": 1},
        { "start_time": 8.401, "end_time": 14.295, "word": "hey hello i would like to check with regards to the uh cashback credit card that i saw on your website", "channel": 0},
        { "start_time": 15.685, "end_time": 17.768, "word": "okay uh just before that can i know how can i address you", "channel": 1},
        { "start_time": 18.264, "end_time": 19.065, "word": "my name is mary", "channel": 0},
        { "start_time": 21.023, "end_time": 27.134, "word": "okay um just before that can i ask you uh are you a current credit card uh holder yourself with any other cards", "channel": 1},
        { "start_time": 27.902, "end_time": 28.893, "word": "u not with your bank", "channel": 0}
    ],
    "alternatives": [
        {
            "transcript": "call one banking",
            "words": [
                { "start_time": 1.647, "end_time": 1.687, "word": "call", "confidence": 0.49167794},
                { "start_time": 1.972, "end_time": 2.013, "word": "one", "confidence": 0.9198732},
                { "start_time": 2.461, "end_time": 2.746, "word": "banking", "confidence": 0.99409324}
            ]
        },
        {
            "transcript": "hey hello i would like to check with regards to the uh cashback credit card that i saw on your website",
            "words": [
                { "start_time": 8.401, "end_time": 8.522, "word": "hey", "confidence": 0.95223147},
                { "start_time": 8.683, "end_time": 8.925, "word": "hello", "confidence": 0.92957836},
                { "start_time": 9.41, "end_time": 9.491, "word": "i", "confidence": 0.59532654},
                { "start_time": 9.531, "end_time": 9.571, "word": "would", "confidence": 0.48604336},
                { "start_time": 9.612, "end_time": 9.652, "word": "like", "confidence": 0.99993515},
                { "start_time": 9.773, "end_time": 9.814, "word": "to", "confidence": 0.9998481},
                { "start_time": 9.894, "end_time": 9.935, "word": "check", "confidence": 0.99967957},
                { "start_time": 10.137, "end_time": 10.177, "word": "with", "confidence": 0.9999976},
                { "start_time": 10.5, "end_time": 10.742, "word": "regards", "confidence": 0.71822035},
                { "start_time": 10.904, "end_time": 10.944, "word": "to", "confidence": 0.9999819},
                { "start_time": 11.388, "end_time": 11.429, "word": "the", "confidence": 0.99984443},
                { "start_time": 11.63, "end_time": 11.792, "word": "uh", "confidence": 0.98684293},
                { "start_time": 11.832, "end_time": 12.236, "word": "cashback", "confidence": 0.78176856},
                { "start_time": 12.357, "end_time": 12.398, "word": "credit", "confidence": 0.99994075},
                { "start_time": 12.68, "end_time": 12.801, "word": "card", "confidence": 0.9999652},
                { "start_time": 13.003, "end_time": 13.043, "word": "that", "confidence": 0.9999608},
                { "start_time": 13.245, "end_time": 13.286, "word": "i", "confidence": 0.99999607},
                { "start_time": 13.488, "end_time": 13.609, "word": "saw", "confidence": 0.9999944},
                { "start_time": 13.689, "end_time": 13.73, "word": "on", "confidence": 0.9999944},
                { "start_time": 13.811, "end_time": 13.851, "word": "your", "confidence": 0.99958616},
                { "start_time": 14.012, "end_time": 14.295, "word": "website", "confidence": 0.9975151}
            ]
        },
        {
            "transcript": "my name is mary",
            "words": [
                { "start_time": 18.264, "end_time": 18.306, "word": "my", "confidence": 0.99948597},
                { "start_time": 18.432, "end_time": 18.475, "word": "name", "confidence": 0.99998605},
                { "start_time": 18.601, "end_time": 18.643, "word": "is", "confidence": 0.9999833},
                { "start_time": 18.812, "end_time": 19.065, "word": "mary", "confidence": 0.38311115}
            ]
        },
        {
            "transcript": "u not with your bank",
            "words": [
                { "start_time": 27.902, "end_time": 27.985, "word": "u", "confidence": 0.47298968},
                { "start_time": 28.274, "end_time": 28.315, "word": "not", "confidence": 0.999495},
                { "start_time": 28.439, "end_time": 28.48, "word": "with", "confidence": 0.99993706},
                { "start_time": 28.646, "end_time": 28.687, "word": "your", "confidence": 0.99987507},
                { "start_time": 28.852, "end_time": 28.893, "word": "bank", "confidence": 0.99991226}
            ]
        },
        {
            "transcript": "call one",
            "words": [
                { "start_time": 0.311, "end_time": 0.353, "word": "call", "confidence": 0.88362044},
                { "start_time": 0.653, "end_time": 0.695, "word": "one", "confidence": 0.9977501}
            ]
        },
        {
            "transcript": "banking",
            "words": [
                { "start_time": 1.73, "end_time": 1.949, "word": "banking", "confidence": 0.99850637}
            ]
        },
        {
            "transcript": "hi thank you for calling a a b c bank uh how may i help you today",
            "words": [
                { "start_time": 5.001, "end_time": 5.082, "word": "hi", "confidence": 0.9683632},
                { "start_time": 5.245, "end_time": 5.326, "word": "thank", "confidence": 0.9998915},
                { "start_time": 5.367, "end_time": 5.407, "word": "you", "confidence": 0.9999645},
                { "start_time": 5.488, "end_time": 5.529, "word": "for", "confidence": 0.99998844},
                { "start_time": 5.651, "end_time": 5.854, "word": "calling", "confidence": 0.9999397},
                { "start_time": 5.935, "end_time": 5.976, "word": "a", "confidence": 0.67438424},
                { "start_time": 6.179, "end_time": 6.22, "word": "a", "confidence": 0.99982893},
                { "start_time": 6.342, "end_time": 6.382, "word": "b", "confidence": 0.99979657},
                { "start_time": 6.464, "end_time": 6.504, "word": "c", "confidence": 0.99938667},
                { "start_time": 6.626, "end_time": 6.667, "word": "bank", "confidence": 0.9999534},
                { "start_time": 6.83, "end_time": 6.992, "word": "uh", "confidence": 0.8405076},
                { "start_time": 7.033, "end_time": 7.073, "word": "how", "confidence": 0.99993837},
                { "start_time": 7.155, "end_time": 7.195, "word": "may", "confidence": 0.9998361},
                { "start_time": 7.195, "end_time": 7.277, "word": "i", "confidence": 0.9859642},
                { "start_time": 7.317, "end_time": 7.358, "word": "help", "confidence": 0.99985945},
                { "start_time": 7.439, "end_time": 7.48, "word": "you", "confidence": 0.99995303},
                { "start_time": 7.683, "end_time": 7.724, "word": "today", "confidence": 0.99999714}
            ]
        },
        {
            "transcript": "okay uh just before that can i know how can i address you",
            "words": [
                { "start_time": 15.685, "end_time": 15.726, "word": "okay", "confidence": 0.9998852},
                { "start_time": 15.807, "end_time": 16.012, "word": "uh", "confidence": 0.9934697},
                { "start_time": 16.093, "end_time": 16.134, "word": "just", "confidence": 0.9999838},
                { "start_time": 16.461, "end_time": 16.502, "word": "before", "confidence": 0.9998858},
                { "start_time": 16.584, "end_time": 16.624, "word": "that", "confidence": 0.9999827},
                { "start_time": 16.747, "end_time": 16.829, "word": "can", "confidence": 0.99248284},
                { "start_time": 16.829, "end_time": 16.87, "word": "i", "confidence": 0.99897337},
                { "start_time": 16.951, "end_time": 16.992, "word": "know", "confidence": 0.99997866},
                { "start_time": 17.074, "end_time": 17.115, "word": "how", "confidence": 0.9999827},
                { "start_time": 17.196, "end_time": 17.237, "word": "can", "confidence": 0.9999832},
                { "start_time": 17.278, "end_time": 17.319, "word": "i", "confidence": 0.99986196},
                { "start_time": 17.36, "end_time": 17.564, "word": "address", "confidence": 0.8864633},
                { "start_time": 17.728, "end_time": 17.768, "word": "you", "confidence": 0.99998796}
            ]
        },
        {
            "transcript": "okay um just before that can i ask you uh are you a current credit card uh holder yourself with any other cards",
            "words": [
                { "start_time": 21.023, "end_time": 21.064, "word": "okay", "confidence": 0.9999542},
                { "start_time": 21.266, "end_time": 21.387, "word": "um", "confidence": 0.99952483},
                { "start_time": 22.359, "end_time": 22.399, "word": "just", "confidence": 0.999977},
                { "start_time": 22.682, "end_time": 22.723, "word": "before", "confidence": 0.9999509},
                { "start_time": 22.844, "end_time": 22.885, "word": "that", "confidence": 0.99999034},
                { "start_time": 23.006, "end_time": 23.047, "word": "can", "confidence": 0.99997246},
                { "start_time": 23.087, "end_time": 23.168, "word": "i", "confidence": 0.69025975},
                { "start_time": 23.209, "end_time": 23.33, "word": "ask", "confidence": 0.9999938},
                { "start_time": 23.37, "end_time": 23.411, "word": "you", "confidence": 0.99989057},
                { "start_time": 23.492, "end_time": 23.694, "word": "uh", "confidence": 0.96052206},
                { "start_time": 23.775, "end_time": 23.816, "word": "are", "confidence": 0.9999256},
                { "start_time": 23.896, "end_time": 23.937, "word": "you", "confidence": 0.99992883},
                { "start_time": 24.058, "end_time": 24.099, "word": "a", "confidence": 0.9999484},
                { "start_time": 24.342, "end_time": 24.382, "word": "current", "confidence": 0.99995613},
                { "start_time": 24.706, "end_time": 24.746, "word": "credit", "confidence": 0.9999758},
                { "start_time": 25.07, "end_time": 25.191, "word": "card", "confidence": 0.9996946},
                { "start_time": 25.232, "end_time": 25.434, "word": "uh", "confidence": 0.97591966},
                { "start_time": 25.556, "end_time": 25.718, "word": "holder", "confidence": 0.99980694},
                { "start_time": 25.798, "end_time": 26.041, "word": "yourself", "confidence": 0.9964786},
                { "start_time": 26.203, "end_time": 26.244, "word": "with", "confidence": 0.99993837},
                { "start_time": 26.365, "end_time": 26.405, "word": "any", "confidence": 0.9999598},
                { "start_time": 26.608, "end_time": 26.648, "word": "other", "confidence": 0.99999416},
                { "start_time": 26.891, "end_time": 27.134, "word": "cards", "confidence": 0.9852808}
            ]
        }
    ]
}

```

### Planned Release (ver. 1.8.0)

```
{
    "status": "success",
    "metadata": {
        "sample_rate": 16000,
        "created": "2023-05-30 09:46:10.857865",
        "duration": 30.016,
        "channels": 2,
        "model": "en-sg"
    },
    "channels": [
        {
            "alternatives": {
                "transcript": "call one banking hey hello i would like to check with regards to the uh cashback credit card that i saw on your website my name is mary u not with your bank",
                "sentences": [
                    {
                        "sentence": "call one banking",
                        "words": [
                            { "startTime": "1.647s", "endTime": "1.687s", "word": "call", "confidence": 0.49167794 },
                            { "startTime": "1.972s", "endTime": "2.013s", "word": "one", "confidence": 0.9198732 },
                            { "startTime": "2.461s", "endTime": "2.746s", "word": "banking", "confidence": 0.99409324 }
                        ]
                    },
                    {
                        "sentence": "hey hello i would like to check with regards to the uh cashback credit card that i saw on your website",
                        "words": [
                            { "startTime": "8.401s", "endTime": "8.522s", "word": "hey", "confidence": 0.95223147 },
                            { "startTime": "8.683s", "endTime": "8.925s", "word": "hello", "confidence": 0.92957836 },
                            { "startTime": "9.410s", "endTime": "9.491s", "word": "i", "confidence": 0.59532654 },
                            { "startTime": "9.531s", "endTime": "9.571s", "word": "would", "confidence": 0.48604336 },
                            { "startTime": "9.612s", "endTime": "9.652s", "word": "like", "confidence": 0.99993515 },
                            { "startTime": "9.773s", "endTime": "9.814s", "word": "to", "confidence": 0.9998481 },
                            { "startTime": "9.894s", "endTime": "9.935s", "word": "check", "confidence": 0.99967957 },
                            { "startTime": "10.137s", "endTime": "10.177s", "word": "with", "confidence": 0.9999976 },
                            { "startTime": "10.500s", "endTime": "10.742s", "word": "regards", "confidence": 0.71822035 },
                            { "startTime": "10.904s", "endTime": "10.944s", "word": "to", "confidence": 0.9999819 },
                            { "startTime": "11.388s", "endTime": "11.429s", "word": "the", "confidence": 0.99984443 },
                            { "startTime": "11.630s", "endTime": "11.792s", "word": "uh", "confidence": 0.98684293 },
                            { "startTime": "11.832s", "endTime": "12.236s", "word": "cashback", "confidence": 0.78176856 },
                            { "startTime": "12.357s", "endTime": "12.398s", "word": "credit", "confidence": 0.99994075 },
                            { "startTime": "12.680s", "endTime": "12.801s", "word": "card", "confidence": 0.9999652 },
                            { "startTime": "13.003s", "endTime": "13.043s", "word": "that", "confidence": 0.9999608 },
                            { "startTime": "13.245s", "endTime": "13.286s", "word": "i", "confidence": 0.99999607 },
                            { "startTime": "13.488s", "endTime": "13.609s", "word": "saw", "confidence": 0.9999944 },
                            { "startTime": "13.689s", "endTime": "13.730s", "word": "on", "confidence": 0.9999944 },
                            { "startTime": "13.811s", "endTime": "13.851s", "word": "your", "confidence": 0.99958616 },
                            { "startTime": "14.012s", "endTime": "14.295s", "word": "website", "confidence": 0.9975151 }
                        ]
                    },
                    {
                        "sentence": "my name is mary",
                        "words": [
                            { "startTime": "18.264s", "endTime": "18.306s", "word": "my", "confidence": 0.99948597 },
                            { "startTime": "18.432s", "endTime": "18.475s", "word": "name", "confidence": 0.99998605 },
                            { "startTime": "18.601s", "endTime": "18.643s", "word": "is", "confidence": 0.9999833 },
                            { "startTime": "18.812s", "endTime": "19.065s", "word": "mary", "confidence": 0.38311115 }
                        ]
                    },
                    {
                        "sentence": "u not with your bank",
                        "words": [
                            { "startTime": "27.902s", "endTime": "27.985s", "word": "u", "confidence": 0.47298968 },
                            { "startTime": "28.274s", "endTime": "28.315s", "word": "not", "confidence": 0.999495 },
                            { "startTime": "28.439s", "endTime": "28.480s", "word": "with", "confidence": 0.99993706 },
                            { "startTime": "28.646s", "endTime": "28.687s", "word": "your", "confidence": 0.99987507 },
                            { "startTime": "28.852s", "endTime": "28.893s", "word": "bank", "confidence": 0.99991226 }
                        ]
                    }
                ]
            }
        },
        {
            "alternatives": {
                "transcript": "call one banking hi thank you for calling a a b c bank uh how may i help you today okay uh just before that can i know how can i address you okay um just before that can i ask you uh are you a current credit card uh holder yourself with any other cards",
                "sentences": [
                    {
                        "sentence": "call one",
                        "words": [
                            { "startTime": "0.311s", "endTime": "0.353s", "word": "call", "confidence": 0.88362044 },
                            { "startTime": "0.653s", "endTime": "0.695s", "word": "one", "confidence": 0.9977501 }
                        ]
                    },
                    {
                        "sentence": "banking",
                        "words": [
                            { "startTime": "1.730s", "endTime": "1.949s", "word": "banking", "confidence": 0.99850637 }
                        ]
                    },
                    {
                        "sentence": "hi thank you for calling a a b c bank uh how may i help you today",
                        "words": [
                            { "startTime": "5.001s", "endTime": "5.082s", "word": "hi", "confidence": 0.9683632 },
                            { "startTime": "5.245s", "endTime": "5.326s", "word": "thank", "confidence": 0.9998915 },
                            { "startTime": "5.367s", "endTime": "5.407s", "word": "you", "confidence": 0.9999645 },
                            { "startTime": "5.488s", "endTime": "5.529s", "word": "for", "confidence": 0.99998844 },
                            { "startTime": "5.651s", "endTime": "5.854s", "word": "calling", "confidence": 0.9999397 },
                            { "startTime": "5.935s", "endTime": "5.976s", "word": "a", "confidence": 0.67438424 },
                            { "startTime": "6.179s", "endTime": "6.220s", "word": "a", "confidence": 0.99982893 },
                            { "startTime": "6.342s", "endTime": "6.382s", "word": "b", "confidence": 0.99979657 },
                            { "startTime": "6.464s", "endTime": "6.504s", "word": "c", "confidence": 0.99938667 },
                            { "startTime": "6.626s", "endTime": "6.667s", "word": "bank", "confidence": 0.9999534 },
                            { "startTime": "6.830s", "endTime": "6.992s", "word": "uh", "confidence": 0.8405076 },
                            { "startTime": "7.033s", "endTime": "7.073s", "word": "how", "confidence": 0.99993837 },
                            { "startTime": "7.155s", "endTime": "7.195s", "word": "may", "confidence": 0.9998361 },
                            { "startTime": "7.195s", "endTime": "7.277s", "word": "i", "confidence": 0.9859642 },
                            { "startTime": "7.317s", "endTime": "7.358s", "word": "help", "confidence": 0.99985945 },
                            { "startTime": "7.439s", "endTime": "7.480s", "word": "you", "confidence": 0.99995303 },
                            { "startTime": "7.683s", "endTime": "7.724s", "word": "today", "confidence": 0.99999714 }
                        ]
                    },
                    {
                        "sentence": "okay uh just before that can i know how can i address you",
                        "words": [
                            { "startTime": "15.685s", "endTime": "15.726s", "word": "okay", "confidence": 0.9998852 },
                            { "startTime": "15.807s", "endTime": "16.012s", "word": "uh", "confidence": 0.9934697 },
                            { "startTime": "16.093s", "endTime": "16.134s", "word": "just", "confidence": 0.9999838 },
                            { "startTime": "16.461s", "endTime": "16.502s", "word": "before", "confidence": 0.9998858 },
                            { "startTime": "16.584s", "endTime": "16.624s", "word": "that", "confidence": 0.9999827 },
                            { "startTime": "16.747s", "endTime": "16.829s", "word": "can", "confidence": 0.99248284 },
                            { "startTime": "16.829s", "endTime": "16.870s", "word": "i", "confidence": 0.99897337 },
                            { "startTime": "16.951s", "endTime": "16.992s", "word": "know", "confidence": 0.99997866 },
                            { "startTime": "17.074s", "endTime": "17.115s", "word": "how", "confidence": 0.9999827 },
                            { "startTime": "17.196s", "endTime": "17.237s", "word": "can", "confidence": 0.9999832 },
                            { "startTime": "17.278s", "endTime": "17.319s", "word": "i", "confidence": 0.99986196 },
                            { "startTime": "17.360s", "endTime": "17.564s", "word": "address", "confidence": 0.8864633 },
                            { "startTime": "17.728s", "endTime": "17.768s", "word": "you", "confidence": 0.99998796 }
                        ]
                    },
                    {
                        "sentence": "okay um just before that can i ask you uh are you a current credit card uh holder yourself with any other cards",
                        "words": [
                            { "startTime": "21.023s", "endTime": "21.064s", "word": "okay", "confidence": 0.9999542 },
                            { "startTime": "21.266s", "endTime": "21.387s", "word": "um", "confidence": 0.99952483 },
                            { "startTime": "22.359s", "endTime": "22.399s", "word": "just", "confidence": 0.999977 },
                            { "startTime": "22.682s", "endTime": "22.723s", "word": "before", "confidence": 0.9999509 },
                            { "startTime": "22.844s", "endTime": "22.885s", "word": "that", "confidence": 0.99999034 },
                            { "startTime": "23.006s", "endTime": "23.047s", "word": "can", "confidence": 0.99997246 },
                            { "startTime": "23.087s", "endTime": "23.168s", "word": "i", "confidence": 0.69025975 },
                            { "startTime": "23.209s", "endTime": "23.330s", "word": "ask", "confidence": 0.9999938 },
                            { "startTime": "23.370s", "endTime": "23.411s", "word": "you", "confidence": 0.99989057 },
                            { "startTime": "23.492s", "endTime": "23.694s", "word": "uh", "confidence": 0.96052206 },
                            { "startTime": "23.775s", "endTime": "23.816s", "word": "are", "confidence": 0.9999256 },
                            { "startTime": "23.896s", "endTime": "23.937s", "word": "you", "confidence": 0.99992883 },
                            { "startTime": "24.058s", "endTime": "24.099s", "word": "a", "confidence": 0.9999484 },
                            { "startTime": "24.342s", "endTime": "24.382s", "word": "current", "confidence": 0.99995613 },
                            { "startTime": "24.706s", "endTime": "24.746s", "word": "credit", "confidence": 0.9999758 },
                            { "startTime": "25.070s", "endTime": "25.191s", "word": "card", "confidence": 0.9996946 },
                            { "startTime": "25.232s", "endTime": "25.434s", "word": "uh", "confidence": 0.97591966 },
                            { "startTime": "25.556s", "endTime": "25.718s", "word": "holder", "confidence": 0.99980694 },
                            { "startTime": "25.798s", "endTime": "26.041s", "word": "yourself", "confidence": 0.9964786 },
                            { "startTime": "26.203s", "endTime": "26.244s", "word": "with", "confidence": 0.99993837 },
                            { "startTime": "26.365s", "endTime": "26.405s", "word": "any", "confidence": 0.9999598 },
                            { "startTime": "26.608s", "endTime": "26.648s", "word": "other", "confidence": 0.99999416 },
                            { "startTime": "26.891s", "endTime": "27.134s", "word": "cards", "confidence": 0.9852808 }
                        ]
                    }
                ]
            }
        }
    ]
}

```

It includes:
  - 'status' section for the status of the job request
  - 'metadata' section containing general information such as transcription creation datatime, audio duration, language, etc
  - 'channels' section where transcription results are stored with the following order
    1. (full) transcript
      - shows the entire transcription text of the entire duration of the audio for the channel
    2. Sentence
      - transcription text of the sentence detected
    3. Word
      - transcription text of the word
      - timestamp, start and end time
      - word level confidence score
      - speaker tag if selected

If there are two or more channels, the entire hierachy of channel, sentence and word level outputs will be repeated accordingly.

