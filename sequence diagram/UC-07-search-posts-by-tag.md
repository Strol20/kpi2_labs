# UC-07 - Пошук дописів за тегом

```mermaid
sequenceDiagram
    autonumber
    actor User as Користувач
    participant UI as ForumUI
    participant TagService as TagService
    participant PostService as PostService
    participant TagRepository as TagRepository
    participant PostRepository as PostRepository

    User->>UI: enterTag("пейзаж")
    User->>UI: searchPostsByTag()
    activate UI
    UI->>TagService: findPostsByTag(name)
    activate TagService
    TagService->>TagRepository: findTagByName(name)
    activate TagRepository
    TagRepository-->>TagService: tagLookupResult(tag_id)
    deactivate TagRepository
    alt Тег знайдено
        TagService->>PostService: findPublishedPostsByTag(tag_id)
        activate PostService
        PostService->>TagRepository: findPostIdsByTagId(tag_id)
        activate TagRepository
        TagRepository-->>PostService: post_ids
        deactivate TagRepository
        PostService->>PostRepository: findPublishedPostsByIds(post_ids)
        activate PostRepository
        PostRepository-->>PostService: matchingPosts
        deactivate PostRepository
        alt Знайдено дописи
            loop Для кожного знайденого допису
                PostService->>PostService: addPostToResults(post)
            end
            PostService-->>TagService: searchResults(matchingPosts)
        else Розширення моделі: дописів із цим тегом немає
            PostService-->>TagService: searchResults([])
        end
        deactivate PostService
        TagService-->>UI: searchResults(results)
    else Розширення моделі: тег не знайдено
        TagService-->>UI: emptySearchResults()
    end
    deactivate TagService
    UI-->>User: displaySearchResults()
    deactivate UI
```

