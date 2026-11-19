# UC-03 - Перегляд опублікованого допису з фотографіями
```mermaid
sequenceDiagram
    autonumber
    actor User as Користувач
    participant UI as ForumUI
    participant PostService as PostService
    participant PostRepository as PostRepository
    participant PhotoRepository as PhotoRepository

    User->>UI: openPost("Захід сонця")
    activate UI
    UI->>PostService: getPost(postId)
    activate PostService
    PostService->>PostRepository: findPostById(postId)
    activate PostRepository
    PostRepository-->>PostService: postFound(post)
    deactivate PostRepository
    alt Допис знайдено та опубліковано
        PostService->>PhotoRepository: findPhotosByPostId(postId)
        activate PhotoRepository
        PhotoRepository-->>PostService: photosList
        deactivate PhotoRepository
        loop Для кожної фотографії допису
            PostService->>PostService: mapPhotoMetadata(photo)
        end
        PostService-->>UI: postDetails(post, photosList)
    else Допис не знайдено або не опубліковано
        PostService-->>UI: postUnavailable()
    end
    deactivate PostService
    UI-->>User: displayPostAndPhotos()
    deactivate UI
```
