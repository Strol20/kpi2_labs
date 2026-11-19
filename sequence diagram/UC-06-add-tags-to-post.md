# UC-06 - Додавання тегів до допису
```mermaid
sequenceDiagram
    autonumber
    actor Photographer as Фотограф
    participant UI as ForumUI
    participant PostService as PostService
    participant TagService as TagService
    participant PostRepository as PostRepository
    participant TagRepository as TagRepository
    participant PostTagRepository as PostTagRepository

    Photographer->>UI: createPost("Захід сонця")
    Photographer->>UI: addTags(["пейзаж", "захід сонця"])
    Photographer->>UI: publishPost()
    activate UI
    UI->>PostService: publishPost(postData, tags)
    activate PostService
    PostService->>PostRepository: savePost(postData)
    activate PostRepository
    PostRepository-->>PostService: postSaved(postId)
    deactivate PostRepository
    loop Для кожного тегу
        PostService->>TagService: getOrCreateTag(tagName)
        activate TagService
        TagService->>TagRepository: findTagByName(tagName)
        activate TagRepository
        TagRepository-->>TagService: tagLookupResult(tag)
        deactivate TagRepository
        alt Тег уже існує
            TagService-->>PostService: existingTag(tagId)
        else Тег ще не існує
            TagService->>TagRepository: saveTag(tagName)
            activate TagRepository
            TagRepository-->>TagService: tagCreated(tagId)
            deactivate TagRepository
            TagService-->>PostService: newTag(tagId)
        end
        deactivate TagService
        PostService->>PostTagRepository: linkTagToPost(postId, tagId)
        activate PostTagRepository
        PostTagRepository-->>PostService: tagLinked()
        deactivate PostTagRepository
    end
    PostService-->>UI: postPublishedWithTags(postId)
    deactivate PostService
    UI-->>Photographer: showPublishedPost()
    deactivate UI
```
