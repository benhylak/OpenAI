# OpenAI

**Response**

```swift
public struct AudioTranslationResult: Codable, Equatable {
    
    public let text: String
}
```

**Example**

```swift
let data = Data(contentsOfURL:...)
let query = AudioTranslationQuery(file: data, fileName: "audio.m4a", model: .whisper_1)  

openAI.audioTranslations(query: query) { result in
    //Handle result here
}
//or
let result = try await openAI.audioTranslations(query: query)
```

Review [Audio Documentation](https://platform.openai.com/docs/api-reference/audio) for more info.

### Edits

Creates a new edit for the provided input, instruction, and parameters.

**Request**

```swift
struct EditsQuery: Codable {
    /// ID of the model to use.
    public let model: Model
    /// Input text to get embeddings for.
    public let input: String?
    /// The instruction that tells the model how to edit the prompt.
    public let instruction: String
    /// The number of images to generate. Must be between 1 and 10.
    public let n: Int?
    /// What sampling temperature to use. Higher values means the model will take more risks. Try 0.9 for more creative applications, and 0 (argmax sampling) for ones with a well-defined answer.
    public let temperature: Double?
    /// An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with top_p probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered.
    public let topP: Double?
}
```

**Response**

```swift
struct EditsResult: Codable, Equatable {
    
    public struct Choice: Codable, Equatable {
        public let text: String
        public let index: Int
    }

    public struct Usage: Codable, Equatable {
        public let promptTokens: Int
        public let completionTokens: Int
        public let totalTokens: Int
        
        enum CodingKeys: String, CodingKey {
            case promptTokens = "prompt_tokens"
            case completionTokens = "completion_tokens"
            case totalTokens = "total_tokens"
        }
    }
    
    public let object: String
    public let created: TimeInterval
    public let choices: [Choice]
    public let usage: Usage
}
```

**Example**

```swift
let query = EditsQuery(model: .gpt4, input: "What day of the wek is it?", instruction: "Fix the spelling mistakes")
openAI.edits(query: query) { result in
  //Handle response here
}
//or
let result = try await openAI.edits(query: query)
```

Review [Edits Documentation](https://platform.openai.com/docs/api-reference/edits) for more info.

### Embeddings

Get a vector representation of a given input that can be easily consumed by machine learning models and algorithms.

**Request**

```swift
struct EmbeddingsQuery: Codable {
    /// ID of the model to use.
    public let model: Model
    /// Input text to get embeddings for
    public let input: String
}
```

**Response**

```swift
struct EmbeddingsResult: Codable, Equatable {

    public struct Embedding: Codable, Equatable {

        public let object: String
        public let embedding: [Double]
        public let index: Int
    }
    public let data: [Embedding]
    public let usage: Usage
}
```

**Example**

```swift
let query = EmbeddingsQuery(model: .textSearchBabbageDoc, input: "The food was delicious and the waiter...")
openAI.embeddings(query: query) { result in
  //Handle response here
}
//or
let result = try await openAI.embeddings(query: query)
```

```
(lldb) po result
▿ EmbeddingsResult
  ▿ data : 1 element
    ▿ 0 : Embedding
      - object : "embedding"
      ▿ embedding : 2048 elements
        - 0 : 0.0010535449
        - 1 : 0.024234328
        - 2 : -0.0084999
        - 3 : 0.008647452
    .......
        - 2044 : 0.017536353
        - 2045 : -0.005897616
        - 2046 : -0.026559394
        - 2047 : -0.016633155
      - index : 0

(lldb)
```

Review [Embeddings Documentation](https://platform.openai.com/docs/api-reference/embeddings) for more info.

### Models 

Models are represented as a typealias `typealias Model = String`.

```swift
public extension Model {
    static let gpt4 = "gpt-4"
    static let gpt4_0314 = "gpt-4-0314"
    static let gpt4_32k = "gpt-4-32k"
    static let gpt4_32k_0314 = "gpt-4-32k-0314"
    static let gpt3_5Turbo = "gpt-3.5-turbo"
    static let gpt3_5Turbo0301 = "gpt-3.5-turbo-0301"
    
    static let textDavinci_003 = "text-davinci-003"
    static let textDavinci_002 = "text-davinci-002"
    static let textCurie = "text-curie-001"
    static let textBabbage = "text-babbage-001"
    static let textAda = "text-ada-001"
    
    static let textDavinci_001 = "text-davinci-001"
    static let codeDavinciEdit_001 = "code-davinci-edit-001"
    
    static let whisper_1 = "whisper-1"
    
    static let davinci = "davinci"
    static let curie = "curie"
    static let babbage = "babbage"
    static let ada = "ada"
    
    static let textEmbeddingAda = "text-embedding-ada-002"
    static let textSearchAda = "text-search-ada-doc-001"
    static let textSearchBabbageDoc = "text-search-babbage-doc-001"
    static let textSearchBabbageQuery001 = "text-search-babbage-query-001"
    
    static let textModerationStable = "text-moderation-stable"
    static let textModerationLatest = "text-moderation-latest"
    static let moderation = "text-moderation-001"
}
```

GPT-4 models are supported. 

For example to use basic GPT-4 8K model pass `.gpt4` as a parameter.

```swift
let query = ChatQuery(model: .gpt4, messages: [
    .init(role: .system, content: "You are Librarian-GPT. You know everything about the books."),
    .init(role: .user, content: "Who wrote Harry Potter?")
])
let result = try await openAI.chats(query: query)
XCTAssertFalse(result.choices.isEmpty)
```

You can also pass a custom string if you need to use some model, that is not represented above.

#### List Models

Lists the currently available models.

**Response**

```swift
public struct ModelsResult: Codable, Equatable {
    
    public let data: [ModelResult]
    public let object: String
}

```
**Example**

```swift
openAI.models() { result in
  //Handle result here
}
//or
let result = try await openAI.models()
```

#### Retrieve Model

Retrieves a model instance, providing ownership information.

**Request**

```swift
public struct ModelQuery: Codable, Equatable {
    
    public let model: Model
}    
```

**Response**

```swift
public struct ModelResult: Codable, Equatable {

    public let id: Model
    public let object: String
    public let ownedBy: String
}
```

**Example**

```swift
let query = ModelQuery(model: .gpt4)
openAI.model(query: query) { result in
  //Handle result here
}
//or
let result = try await openAI.model(query: query)
```

Review [Models Documentation](https://platform.openai.com/docs/api-reference/models) for more info.

### Moderations 

Given a input text, outputs if the model classifies it as violating OpenAI's content policy.

**Request**

```swift
public struct ModerationsQuery: Codable {
    
    public let input: String
    public let model: Model?
}    
```

**Response**

```swift
public struct ModerationsResult: Codable, Equatable {

    public let id: String
    public let model: Model
    public let results: [CategoryResult]
}
```

**Example**

```swift
let query = ModerationsQuery(input: "I want to kill them.")
openAI.moderations(query: query) { result in
  //Handle result here
}
//or
let result = try await openAI.moderations(query: query)
