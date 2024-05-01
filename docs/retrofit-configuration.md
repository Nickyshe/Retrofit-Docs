Retrofit is the class through which your API interfaces are turned into callable objects. While Retrofit offers reasonable defaults, you can customize it for your project's specific requirements.

## Converters
By default, Retrofit can only deserialize HTTP bodies into OkHttp's `ResponseBody` type and it can only accept its `RequestBody` type for `@Body.`

Converters can be added to support other types. Sibling modules adapt popular serialization libraries for your convenience.

- **Gson**: com.squareup.retrofit2:converter-gson
- **Jackson**: com.squareup.retrofit2:converter-jackson
- **Mosh**: com.squareup.retrofit2:converter-moshi
- **Protobuf**: com.squareup.retrofit2:converter-protobuf
- **Wire**: com.squareup.retrofit2:converter-wire
- **Simple XML**: com.squareup.retrofit2:converter-simplexml
- **JAXB**: com.squareup.retrofit2:converter-jaxb
- **Scalars (primitives, boxed, and String)**: com.squareup.retrofit2:converter-scalars

Here's an example of using the GsonConverterFactory class to generate an implementation of the GitHubService interface which uses Gson for its deserialization.

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .addConverterFactory(GsonConverterFactory.create())
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```
## Custom Converters

If you need to communicate with an API that uses a content-format that Retrofit does not support out of the box (e.g. YAML, txt, custom format) or you wish to use a different library to implement an existing format, you can easily create your own converter. 

Create a class that extends the Converter.Factory class and pass in an instance when building your adapter.
