# UC-02 - Публікація допису з фотографіями
```mermaid
sequenceDiagram
    autonumber
    actor Photographer as Фотограф
    participant UI as ForumUI
    participant PostService as PostService
    participant PhotoStorage as PhotoStorageService
    participant TopicRepository as TopicRepository
    participant PostRepository as PostRepository
    participant PhotoRepository as PhotoRepository

    Photographer->>UI: createTopicAndPost("Захід сонця")
    Photographer->>UI: attachPhotos(["sunset-1.jpg", "sunset-2.jpg"])
    Photographer->>UI: submitPost()
    activate UI
    UI->>PostService: publishPost(topicData, postData, photos)
    activate PostService
    PostService->>PostService: validatePost()
    PostService->>TopicRepository: saveTopic(title, content, author_id)
    activate TopicRepository
    TopicRepository-->>PostService: topicSaved(topic_id)
    deactivate TopicRepository
    PostService->>PostRepository: savePost(topic_id, author_id, text, status="draft")
    activate PostRepository
    PostRepository-->>PostService: postSaved(post_id)
    deactivate PostRepository

    loop Для кожної прикріпленої фотографії
        PostService->>PhotoStorage: uploadPhoto(photo_file)
        activate PhotoStorage
        alt Фотографію збережено
            PhotoStorage-->>PostService: returnImageUrl(image_url)
            PostService->>PhotoRepository: savePhoto(post_id, image_url, uploader_id)
            activate PhotoRepository
            PhotoRepository-->>PostService: photoSaved(photo_id)
            deactivate PhotoRepository
        else Помилка збереження фотографії
            PhotoStorage-->>PostService: uploadFailed(error)
            PostService->>PostService: recordUploadFailure(error)
        end
        deactivate PhotoStorage
    end
    alt Усі фотографії збережено
        PostService->>PostRepository: markPostPublished(post_id)
        activate PostRepository
        PostRepository-->>PostService: postPublished()
        deactivate PostRepository
        PostService-->>UI: publicationSucceeded(post_id)
        UI-->>Photographer: showPostWithPhotos(post_id)
    else Помилка збереження хоча б однієї фотографії
        Note over PostService,PostRepository: Допис залишається зі status="draft"
        PostService-->>UI: publicationFailed(post_id, error)
        UI-->>Photographer: showPublicationFailure()
    end
    deactivate PostService
    deactivate UI
```