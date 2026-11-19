# UC-05 - Перегляд коментарів до допису
```mermaid
sequenceDiagram
    autonumber
    actor User as Користувач
    participant UI as ForumUI
    participant CommentService as CommentService
    participant PostRepository as PostRepository
    participant CommentRepository as CommentRepository

    User->>UI: openPost(postId)
    activate UI
    UI->>CommentService: getComments(postId)
    activate CommentService
    CommentService->>PostRepository: findPostById(postId)
    activate PostRepository
    PostRepository-->>CommentService: postFound(post)
    deactivate PostRepository
    alt Допис існує
        CommentService->>CommentRepository: findCommentsByPostId(postId)
        activate CommentRepository
        CommentRepository-->>CommentService: commentsList
        deactivate CommentRepository
        alt Є коментарі
            loop Для кожного коментаря
                CommentService->>CommentService: prepareCommentView(comment)
            end
            CommentService-->>UI: commentsFound(commentsList)
        else Коментарів поки немає
            CommentService-->>UI: emptyCommentsList()
        end
    else Допис не знайдено
        CommentService-->>UI: postNotFound()
    end
    deactivate CommentService
    UI-->>User: displayComments()
    deactivate UI
```

