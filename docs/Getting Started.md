## Requirement
Retrofit requires at minimum Java 8+ or Android API 21+.

**R8 / PROGUARD**

If you are using R8 the shrinking and obfuscation rules are included automatically.

ProGuard users must manually add the options from [**retrofit2.pro.**](https://github.com/square/retrofit/blob/trunk/retrofit/src/main/resources/META-INF/proguard/retrofit2.pro)

You might also need rules for [**OkHttp**](https://square.github.io/okhttp/features/r8_proguard/) and [**Okio**](https://github.com/square/okio#r8--proguard) which are dependencies of this library.


## **Setting Up Retrofit**

Retrofit simplifies making network calls in Android applications. Here's a step-by-step guide to set it up:

### **Add Retrofit as a dependency**
- Open your project's `build.gradle` file (located in the `app` module).
- Add the following dependencies in the `dependencies` block:

  ```Gradle
  dependencies {
      // Other dependencies
      implementation 'com.squareup.retrofit2:retrofit:2.9.0'
      implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
  }
  ```
Replace `2.9.0` with the latest stable version if needed (check Retrofit documentation for updates).

Sync your project with Gradle to download the libraries.

**Maven**
```
<dependency>
  <groupId>com.squareup.retrofit2</groupId>
  <artifactId>retrofit</artifactId>
  <version>(insert latest version)</version>
</dependency>
```



### **Create a Retrofit Instance**

You need a `Retrofit` object to make network requests. Here's how to create one:

In this example, you would use the generated `GitHubService` object:

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .build();

GitHubService service = retrofit.create(GitHubService.class);

Call<List<Repo>> repos = service.listRepos("octocat");
```

This code creates a `Retrofit` instance with the base URL of the GitHub API. Then, it uses this instance to create a `GitHubService` object. Finally, you can call the `listRepos` method on the service, passing the username "octocat" as an argument. This initiates a network request to retrieve the list of repositories for that user.
### Define an API Interface

Create an interface that defines your API endpoints. This interface acts as a contract for your network calls.


Here's an example:

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```

This interface, `GitHubService`, defines a single method, `listRepos`. The `@GET` annotation tells Retrofit that this method makes a GET request. 

The `{user}` part in the URL indicates a placeholder that will be replaced with a specific username when the call is made. The `@Path("user")` annotation links the `user` parameter in the method with the `{user}` placeholder.

Once you've defined the interface, Retrofit generates an object that implements it at runtime. This object handles all the heavy lifting of making the network request, converting the response data into your desired format (like a list of repositories in this case), and notifying you of any errors.



### **Key Features of Retrofit Annotations**

* **URL parameter replacement:** You can use placeholders in the URL to represent dynamic parts, like the username in the example.
* **Query parameter support:** Annotations allow you to specify additional parameters that are appended to the URL as a query string.
* **Object conversion:** Retrofit can automatically convert objects to JSON or other data formats for request bodies and parse responses back into your desired Java classes.
* **Multipart request body and file upload:** Retrofit supports building complex requests with multiple parts, including file uploads.


## **API Declarations with Annotations**

Retrofit uses annotations on your interface methods to understand how to interact with the web API. These annotations basically act as instructions, telling Retrofit what kind of request to make and how to handle the data.

### **Choosing the Right Request Method**

The first thing Retrofit needs to know is the type of HTTP request you want to make. There are eight built-in annotations for this purpose:

* `@GET`: Used for retrieving data from the server.
* `@POST`: Used for sending data to create something on the server (e.g., a new user).
* `@PUT`: Used for updating existing data on the server.
* `@PATCH`: Used for partial updates of data on the server.
* `@DELETE`: Used for deleting data from the server.
* `@OPTIONS`: Used to retrieve information about the communication options of the server.
* `@HEAD`: Used to retrieve only the header information of a request (without the actual data).

You'll place the appropriate annotation at the beginning of your interface method, followed by the relative URL of the resource you want to access. Here's an example using `@GET`:

```java
@GET("users/list")
```

This tells Retrofit to make a GET request to the "/users/list" endpoint on the server.

### **Adding Details with Query Parameters**

You can also include additional information in your request using query parameters. These are appended to the URL as a question mark (?) followed by key-value pairs separated by ampersands (&). In Retrofit, you can specify these directly in the URL within the annotation.

For instance, you might want to retrieve a list of users sorted by descending order:

```java
@GET("users/list?sort=desc")
```

This adds a query parameter named "sort" with the value "desc" to the request URL.


### **URL Manipulation**

Retrofit lets you build URLs that adapt to your needs. You can use replacement blocks enclosed in curly braces `{}` within the URL. These blocks are then filled with corresponding parameters annotated with `@Path`. 

For example:

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId);
```

Here, `@GET("group/{id}/users")` defines the URL pattern. When you call `groupList(123)`, Retrofit replaces `{id}` with the value `123`, resulting in a request to "group/123/users".

**Adding Query Parameters**

You can include additional information in your requests using query parameters. These appear as key-value pairs appended to the URL with a question mark (`?`). Retrofit allows you to specify them directly within the URL of your annotated method:

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
```

This example retrieves a list of users from group `groupId` sorted by the value of the `sort` parameter.

For complex scenarios with many query parameters, you can use a `Map<String, String>` annotated with `@QueryMap`.

### **Sending Data in the Request Body**

You can include data in the request body using the `@Body` annotation. This is typically used for creating new resources on the server. The object you provide is automatically converted using a converter configured for your Retrofit instance. If no converter is specified, you can only use the `RequestBody` class.

Here's an example of creating a new user:

```java
@POST("users/new")
Call<User> createUser(@Body User user);
```

### **Form-Encoded and Multipart Requests**

Retrofit also supports sending data in other formats:

* **Form-encoded data:** Use the `@FormUrlEncoded` annotation on the method and `@Field` annotations on individual parameters to specify key-value pairs.
```java
@FormUrlEncoded
@POST("user/edit")
Call<User> updateUser(@Field("first_name") String first, @Field("last_name") String last);

```

* **Multipart requests:** Use the `@Multipart` annotation for methods that handle data with multiple parts, like file uploads. Each part is declared with the `@Part` annotation.

```java
@Multipart
@PUT("user/photo")
Call<User> updateUser(@Part("photo") RequestBody photo, @Part("description") RequestBody description);

```

### **Managing Request Headers**

You can set headers for your requests using annotations:

* **Static headers:** Use the `@Headers` annotation to define static headers for a specific method.

```java
@Headers("Cache-Control: max-age=640000")
@GET("widget/list")
Call<List<Widget>> widgetList();
```
* **Dynamic headers:** Use the `@Header` annotation with a corresponding parameter to set headers dynamically. You can also use a `Map<String, String>` with `@HeaderMap` for complex scenarios.

```java
@Headers({
    "Accept: application/vnd.github.v3.full+json",
    "User-Agent: Retrofit-Sample-App"
})
@GET("users/{username}")
Call<User> getUser(@Path("username") String username);
```

### **Synchronous vs. Asynchronous Calls**

Retrofit calls can be executed either synchronously (blocking the current thread) or asynchronously. Each `Call` instance can only be used once, but you can create a copy using the `clone()` method.

**Kotlin Support**

Retrofit offers special features for Kotlin development:

* **Suspend functions:** Interface methods can be suspend functions returning a `Response` object directly. This allows asynchronous execution while suspending the current function.
```java
@GET("users")
suspend fun getUser(): Response<User>
```

* **Direct body return:** Suspend functions can also return the response body directly. If the response code is not successful (2XX), an `HttpException` containing the response will be thrown.
```java
@GET("users")
suspend fun getUser(): User
```


## **Retrofit Configuration**
Retrofit is the class through which your API interfaces are turned into callable objects. While Retrofit offers reasonable defaults, you can customize it for your project's specific requirements.

### **Converters**
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
### **Custom Coverters**

If you need to communicate with an API that uses a content-format that Retrofit does not support out of the box (e.g. YAML, txt, custom format) or you wish to use a different library to implement an existing format, you can easily create your own converter. 

Create a class that extends the Converter.Factory class and pass in an instance when building your adapter.
