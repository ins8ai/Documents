## Considerations for Database Design (WIP):

Because of the varied use cases/needs/objectives/context/constraints of different organisations and teams,
it is impossible to have a single reference approach for storing transcripts. This document aims to capture relevant considerations and will be continously updated.

### Table Schemas

The following is an example database table schemas for storing transcripts.

#### Transcripts

Table Name: Transcripts

| transcript_id | audio_file_id | transcription_text                    | datetime                | user_ids       | duration | transcription_json |
|---------------|---------------|---------------------------------------|-------------------------|----------------|----------|--------------------|
| 1             | 101           | "Hello, how are you? ..."             | 2023-05-14 10:23:45.000 | [101, 102]     | 00:01:30 | { ... }            |
| 2             | 102           | "Welcome to our meeting today..."     | 2023-05-14 11:05:30.000 | [103, 104]     | 00:02:15 | { ... }            |
| 3             | 103           | "Good morning, everyone! "            | 2023-05-14 09:15:00.000 | [105]          | 00:00:45 | { ... }            |

Columns:

- `transcript_id`: A unique identifier for each transcript.
- `audio_file_id`: A foreign key referencing the audio file associated with the transcript.
- `transcription_text`: The actual text of the transcript of the entire audio file.
- `datetime`: Timestamp indicating when the transcript was created in YYYY-MM-DD HH:MM:SS.SSS format.
- `user_ids`: A list storing the ids of speakers associated with the transcript.
- `duration`: duration of the audio file associated with the transcript in `HH:MM:SS` format.
- `transcription_json`: raw transcription output containing the timestamps of each individual word and other related variables.

##### Transcript Versioning

Updates to a transcript may be significant and worth preserving. Ammended transcripts can be added to the above table with datetime reflecting when the ammendments were made.
Hence one audio_file_id can have multiple transcripts having different transcription_text and datetime values.


There are then two supporting tables for the above - AudioFiles and Users

#### AudioFiles

Table Name: AudioFiles

| audio_file_id | datetime                | source_system_name | user_ids       | filename    |
|---------------|-------------------------|--------------------|----------------|-------------|
| 101           | 2023-05-14 10:00:00.000 | System A           | [101, 102]     | file101.wav |
| 102           | 2023-05-14 11:00:00.000 | System B           | [103, 104]     | file102.wav |
| 103           | 2023-05-14 09:00:00.000 | System C           | [105]          | file103.wav |

Columns:

- `audio_file_id`: A unique identifier for each audio file.
- `datetime`: Timestamp indicating when the audio was recorded.
- `source_system_name`: The identifier of the system from which the audio clip was sourced from.
- `user_ids`: A list or array-like structure storing the user IDs included in the audio file. e.g. relationship manager, customer identifier.
- `filename`: File path and file names of the audio file. Because audio files are large, they are usually maintained in its original file format. We are only storing filepath and filenames rather than the raw audio content in binary form.

The `user_ids` column allows for storing multiple user IDs associated with an audio file, representing users who are part of or relevant to the audio content.

#### Users

Table Name: Users

| user_id | department_id | user_classification    |
|---------|---------------|------------------------|
| 101     | 201           | Customer               |
| 102     | 201           | Relationship Manager   |
| 103     | 202           | Customer Service Agent |
| 104     | 203           | Relationship Manager   |
| 105     | 202           | Customer               |
| 106     | 204           | Customer Service Agent |

Columns: 

- `user_id`: A unique identifier for each user. These users are speakers of the audio recordings in the AudioFiles table and Transcriptions table. 
- `department_id`: An identifier for the user's department.
- `user_classification`: Represents the classification or role of the user, such as "Customer," "Relationship Manager," or "Customer Service Agent."

This table will most likely be a snapshot extracted from other source tables/systems which maintain user information.

### Transcript Versioning

Updates to a transcript may be significant and worth preserving. Ammended transcripts can be added to the above table with datetime reflecting when the ammendments were made.
Hence one audio_file_id can have multiple transcripts having different transcription_text and datetime values.

While versioning might be worthwhile, it may be equally desirable to maintain a single source of truth for transcripts generated.
To address this, a separate maintainance script to drop outdated records may be run regularly to maintain one transcription result for each audio file.


### Database Paritioning

Main paritioning strategies include:

- Horizontal: dividing/sharding rows of records according to different criterias, for e.g. date time based value.

- Vertical: paritionining information in a columnar manner, for e.g. placing infrequently accessed or information dense columns such as 'transcription_json' column into a separate partition.

- Functional: maintaining different tables for different business units or departments; this facilitates data isolation and data access performance. 

The choice of paritioning strategy depends on the stack and technologies used.
Different technologies will support different paritioning strategies.

### Privacy

Depending on jurisdictions, there may be legal and ethical constraints on storing transcription data which may contain personally identifiable information (PII).
Care should be taken to ensure that the database schema and data handling procedures are in compliance with all relevant laws and best practices.

Transcriptions generated may undergo a user/entity detection and replacement/removal step before downstream analysis/processing is done.
Entity detection and pii removal is a feature planned for upcoming release of the ins8.ai product.

