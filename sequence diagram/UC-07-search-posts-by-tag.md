# UC-07 - Пошук дописів за тегом

```mermaid
sequenceDiagram
    autonumber
    actor User as Користувач
    participant UI as ForumUI
    participant SearchService as PostSearchService
    participant TagRepository as TagRepository
    participant PostTagRepository as PostTagRepository
    participant PostRepository as PostRepository

    User->>UI: enterTag("пейзаж")
    User->>UI: searchPostsByTag()
    activate UI
    UI->>SearchService: findPostsByTag("пейзаж")
    activate SearchService
    SearchService->>TagRepository: findTagByName("пейзаж")
    activate TagRepository
    TagRepository-->>SearchService: tagLookupResult(tag)
    deactivate TagRepository
    alt Тег знайдено
        SearchService->>PostTagRepository: findPostIdsByTagId(tagId)
        activate PostTagRepository
        PostTagRepository-->>SearchService: postIds
        deactivate PostTagRepository
        SearchService->>PostRepository: findPublishedPostsByIds(postIds)
        activate PostRepository
        PostRepository-->>SearchService: matchingPosts
        deactivate PostRepository
        alt Знайдено дописи
            loop Для кожного знайденого допису
                SearchService->>SearchService: addPostToResults(post)
            end
            SearchService-->>UI: searchResults(matchingPosts)
        else Дописів із цим тегом немає
            SearchService-->>UI: emptySearchResults()
        end
    else Тег не знайдено
        SearchService-->>UI: emptySearchResults()
    end
    deactivate SearchService
    UI-->>User: displaySearchResults()
    deactivate UI
```

