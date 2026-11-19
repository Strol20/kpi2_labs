# UC-04 - Додавання коментаря до допису
```mermaid
sequenceDiagram
    autonumber
    actor User as Користувач
    participant UI as ForumUI
    participant CommentService as CommentService
    participant PostRepository as PostRepository
    participant CommentRepository as CommentRepository

    User->>UI: writeComment(postId, "Чудове світло!")
    User->>UI: submitComment()
    activate UI
    UI->>CommentService: addComment(postId, authorId, text)
    activate CommentService
    CommentService->>CommentService: validateComment()
    CommentService->>PostRepository: findPostById(postId)
    activate PostRepository
    PostRepository-->>CommentService: postFound(post)
    deactivate PostRepository
    alt Допис існує та коментар коректний
        CommentService->>CommentRepository: saveComment(postId, authorId, text)
        activate CommentRepository
        CommentRepository-->>CommentService: commentSaved(commentId)
        deactivate CommentRepository
        CommentService-->>UI: commentAdded(commentId)
    else Допис відсутній або текст некоректний
        CommentService-->>UI: commentRejected(reason)
    end
    deactivate CommentService
    UI-->>User: displayCommentResult()
    deactivate UI
```