📱 Mobile System Design – Facebook News Feed (Android)

<img width="468" height="360" alt="ChatGPT Image Jan 5, 2026, 02_23_00 PM" src="https://github.com/user-attachments/assets/9d107cb6-0f69-4ea3-b573-27f31251078d" />

🔹 Gathering Requirements

Before jumping into design, we clarify the problem scope by identifying:
 
      Functional Requirements
      Non-Functional Requirements
      Out of Scope items

This helps align expectations and avoids over-engineering.

1️⃣ Functional Requirements (FR)
   Functional Requirements define what the application should do.
   
   Core Features: 
          
        User can view a list of feed posts
        Each post can contain text, image, or video
   User can:
           
      Like / unlike a post
      View like & comment count
      Add a comment
      Navigate to comments screen/dialog
      Feed supports infinite scrolling (pagination)
      User receives push notifications for interactions (likes/comments)

These define the core user experience of the News Feed.

2️⃣ Non-Functional Requirements (NFR)
   Non-Functional Requirements define how well the system should perform.

Performance & Scale:
    
    System should support ~100M Daily Active Users
    Feed should load within acceptable latency (<2s on good network)
    Smooth scrolling with no UI jank
   
Network & Reliability:

    Graceful handling of poor or intermittent network
    Offline viewing of previously loaded feed
    Background sync when network is restored
    
Data & Efficiency:

    Efficient pagination and caching
    Conflict handling for offline likes/comments
    Minimal battery and data usage
   
Security & Accessibility:
    
    Secure communication via HTTPS
    Accessibility support (TalkBack, content descriptions)


3️⃣ Out of Scope (OOS)
   The following features are intentionally excluded from this design:

    Creating or uploading a post
    Authentication & authorization 
    One-to-one or group chat
    Friend request / social graph management
    iOS / Web clients
    Admin or moderation tools

This design focuses only on feed consumption & interaction.

4️⃣ API & Data Model (High-Level Contract)
 
    Communication
    Protocol: HTTP
    Style: REST APIs
    Data Format: JSON

API - High Level Contract

 📌 Feed API

    GET /v1/feed?page=1&limit=20
    
    Response
    List<Post>
    Post(PostId,createdAt,content,author,postType,likeCount,commentCount)
📌 Comments API
   
    GET /v1/feed/{postId}/comments?page=1&limit=20
    
    Response - Post(postId,createdAt,content,author,type,listOf(Comments))
            Content(title,description,url,image, etc…)
            Author(id,name,username,avatarUrl,activeAt,etc…)
            PostType(type,description,etc…)
            Comments( comments_description, comments_reaction if any, comments_id)

 Returns latest comments for a post.

📌 Like / Reaction API

     POST /v1/feed/{postId}/like

     Request - (postId/commentId,userInteractionType)
            UserInterationType(like,unlike,Heart,Break heart,etc…)
     Response - POST(likeCount,likedAt,type,etc…)

📌 Like / Comments API

     POST/V1/feed/{postId}/comments
     
     Request - (postId/commentId,content,commentedAt)
            Content(title,description,url,image, etc…)
     Response - List<Comments> 
            Comments( comments_description, comments_reaction if any, comments_id,commentedAt)



5️⃣ 💼 High Level Design 
            
<img width="655" height="364" alt="Screenshot 2026-01-05 at 1 21 50 AM" src="https://github.com/user-attachments/assets/e4478186-10a9-4465-93fb-572136d12bbc" />


6️⃣ High-Level Architecture (Mobile Perspective)

      UI (Compose)
       ↓
      ViewModel
       ↓
      Use Cases
       ↓
      Repository
       ↓
      Remote API / Local Cache


 <img width="667" height="574" alt="Screenshot 2026-01-05 at 1 23 33 AM" src="https://github.com/user-attachments/assets/52e6a79c-6759-4da5-99a7-e99897cce278" />
 <img width="651" height="344" alt="Screenshot 2026-01-05 at 1 24 21 AM" src="https://github.com/user-attachments/assets/626be450-8ab9-4053-9892-fdb721efc89f" />


 🔥 7️⃣ Caching Strategy (Must for Mobile)
    
    Client-Side Caching
    
    Cache feed data in local database (Room)
    Images & videos served via CDN
    Cache eviction: 
    LRU or time-based expiry
    Cached data shown immediately, then refreshed


🔥 8️⃣ Offline Actions & Sync (Very Interview-Focused)
   
    Offline Interaction Handling 
    
    Likes/comments stored locally when offline
    Marked as pending
    Synced when network is restored
    Server resolves conflicts using:
    Last-write-wins
    Idempotent APIs


 🔥 9️⃣ Accessibility 
   
     TalkBack support for feed items
     Content descriptions for media
     Proper focus order
     Accessible pagination feedback


  🔥 5️⃣ Error Handling & Retry Strategy
     
     Retry with exponential backoff
     Graceful UI states: Loading , Empty , Error
     Partial failure handling (e.g., feed loads but comments fail)

     
Client-Level Design (Android)

    Language: Kotlin
    UI: Jetpack Compose
    Architecture: Clean Architecture + MVVM
    Navigation: Compose Navigation
    DI: Hilt (Dagger)
    Async: Coroutines + Flow
    Pagination: Paging 3
    Local Cache: Room
    Image Loading: CDN-backed URLs      
      
      
      



