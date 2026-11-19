# UC-01 - Публікація допису без фотографій
```mermaid
sequenceDiagram
    autonumber
    actor Photographer as Фотограф
    participant UI as ForumUI
    participant PostService as PostService
    participant PostRepository as PostRepository

    Photographer->>UI: createPost()
    activate UI
    UI->>PostService: publishPost(title, text, authorId)
    activate PostService
    PostService->>PostService: validatePost()
    alt Дані допису коректні
        PostService->>PostRepository: savePost(post)
        activate PostRepository
        PostRepository-->>PostService: postSaved(postId)
        deactivate PostRepository
        PostService-->>UI: publicationSucceeded(postId)
    else Дані допису некоректні
        PostService-->>UI: publicationRejected(validationErrors)
    end
    deactivate PostService
    UI-->>Photographer: showPublicationResult()
    deactivate UI
```
