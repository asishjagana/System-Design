# Design YouTube / Netflix (Video Streaming)

How to design a scalable video streaming platform.

## 1. Requirements
- **Upload**: Small/Large videos.
- **Streaming**: Smooth playback without buffering.
- **Search**: Finding videos.
- **Analytics**: Views, Likes, Comments.

## 2. Components
- **Transcoding Service**: Converting uploaded videos into different formats (MPEG-DASH, HLS) and resolutions (360p, 720p, 1080p, 4K).
- **CDN (Content Delivery Network)**: Storing and serving video data from servers physically close to the user.
- **Distributed File System**: (e.g., S3, Google Cloud Storage) to store the raw and processed video files.

## 3. Workflow (The Path of a Video)
1. **Upload**: User uploads `.mp4`.
2. **Queue**: Video sits in a queue (Kafka/RabbitMQ).
3. **Transcoding**: Worker nodes pick up the video and convert it.
4. **Storage**: Multiple files (different qualities) are saved to S3.
5. **CDN Propagation**: Files are pushed/pulled to edge locations (Akamai, CloudFront).
6. **Playback**: Users request the "Manifest File" (M3U8) which tells the player where to fetch segments based on network speed.

## 4. Real-Time Use Case
**Netflix**: They use Open Connect (their own CDN) and host their infrastructure on AWS. They use an adaptive bitrate algorithm to change video quality during playback if your internet dips.

## 5. Key Design Point: Throughput vs Storage
- **Storage** is cheap; **Throughput** (bandwidth) is expensive.
- CDNs are the most critical part of the design to ensure users in India don't experience lag for a video hosted in the USA.
