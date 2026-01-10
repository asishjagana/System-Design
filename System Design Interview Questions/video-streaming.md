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

## Advanced Concept: Dynamic Adaptive Streaming over HTTP (DASH / HLS)
The video is broken into 2-5 second segments. The player fetches the next segment based on current bandwidth.
- If speed drops, player fetches "Low Quality" segments.
- if speed is high, it fetches "4K" segments.
This is why Netflix quality might look blurry for a few seconds when you start, then becomes crisp.

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Global Reach**: CDNs ensure users anywhere can watch without lag. | **Storage Costs**: Storing a single movie in 10 different resolutions and formats takes a lot of space. |
| **Resilience**: If one CDN node fails, the player automatically fails over to another. | **Transcoding Time**: Processing a 4K movie takes significant compute time and power. |
| **Smooth Playback**: Adaptive bitrate prevents constant "Buffering" wheels. | |

## Real-Time Use Case
**Netflix**: They use Open Connect (their own CDN) and host their infrastructure on AWS. They use an adaptive bitrate algorithm to change video quality during playback if your internet dips.
