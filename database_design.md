## Considerations for Database Design (WIP):

Due to the varied use cases/needs/objectives/context/constraints of different organisations/teams, it is difficult to have a single reference approach for storing transcripts.
This document aims to capture relevant considerations and will be updated.

### Table Schemas

The following describe database table schemas for storing transcripts.

They describe four tables:

- Transcripts: Stores transcripts of each audio file as individual records
- TranscriptDetails: Stores detailed transcripts where transcripts of each recording are split by time stamps and user id - essentially a flattened/exploded view of transcription_json column of Transcripts table.
- AudioFiles: Stores file path and details of audio files to be transcribed
- Users: Stores information about users

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

#### Transcription Details

Table Name: TranscriptDetails

| transcript_detail_id | transcription_id | start_time | end_time | user_id | sentence                          |
|----------------------|------------------|------------|----------|---------|-----------------------------------|
| 1                    | 1                | 1.0        | 2.0      | 101     | Hello, how are you?               |
| 2                    | 1                | 3.0        | 4.5      | 102     | I'm doing great, thank you!       |
| 3                    | 1                | 4.5        | 5.0      | 101     | That's good to hear.              |
| 5                    | 1                | 5.5        | 6.0      | 102     | What's up?                        |
| 6                    | 1                | 6.0        | 6.7      | 101     | Not much, just chilling.          |
| 7                    | 1                | 6.5        | 7.5      | 102     | I'll talk to you later.           |
| 8                    | 1                | 7.0        | 8.0      | 101     | Catch you later!                  |
| 9                    | 1                | 8.0        | 9.0      | 102     | See you around!                   |


Columns: 

- `transcript_detail_id`: Unique identifier for the transcribed sentence
- `transcription_id`: foreign key referencing the transcription record in the Transcriptions table
- `start_time`: seconds indicating the start of the transcribed sentence
- `end_time`: seconds indicating the end of the transcribed sentence
- `user_id`: foreign key referencing the user in the Users table
- `sentence`: transcribed content


#### Audio Files

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

Depending on data jurisdiction, there may be legal and regulatory constraints on storing transcription data..
Care should be taken to ensure that the database schema and data handling procedures are in compliance with all relevant laws and best practices for example removal of personally identifiable information (PII) data.

Transcriptions generated may undergo a user/entity detection and replacement/removal process before furtheer analysis/processing is done.
Entity detection and pii removal is a feature planned for upcoming release of the ins8.ai product.


## Feedback

To provide feedback on the above, please email developer@ins8.ai