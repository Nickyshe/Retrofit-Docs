Retrofit uses annotations on your interface methods to understand how to interact with the web API. These annotations basically act as instructions, telling Retrofit what kind of request to make and how to handle the data.

## Choosing the Right Request Method

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

This tells Retrofit to make a GET request to the `"/users/list"` endpoint on the server.

## Adding Details with Query Parameters

You can also include additional information in your request using query parameters. These are appended to the URL as a question mark (?). In Retrofit, you can specify these directly in the URL within the annotation.

For instance, you might want to retrieve a list of users sorted by descending order:

```java
@GET("users/list?sort=desc")
```

This adds a query parameter named `"sort"` with the value `"desc"` to the request URL.


## URL Manipulation

Retrofit lets you build URLs that adapt to your needs. You can use replacement blocks enclosed in curly braces `{}` within the URL. These blocks are then filled with corresponding parameters annotated with `@Path`. 

For example:

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId);
```

Here, `@GET("group/{id}/users")` defines the URL pattern. When you call `groupList(123)`, Retrofit replaces `{id}` with the value `123`, resulting in a request to `"group/123/users"`.

**Adding Query Parameters**

You can include additional information in your requests using query parameters. These appear as key-value pairs appended to the URL with a question mark (`?`). Retrofit allows you to specify them directly within the URL of your annotated method:

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
```

This example retrieves a list of users from group `groupId` sorted by the value of the `sort` parameter.

For complex scenarios with many query parameters, you can use a `Map<String, String>` annotated with `@QueryMap`.

## Sending Data in the Request Body
You can include data in the request body using the `@Body` annotation. This is typically used for creating new resources on the server. The object you provide is automatically converted using a converter configured for your Retrofit instance. If no converter is specified, you can only use the `RequestBody` class.

Here's an example of creating a new user:

```java
@POST("users/new")
Call<User> createUser(@Body User user);
```

## Form-Encoded and Multipart Requests

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

## Managing Request Headers

You can set headers for your requests using annotations:

- **Static headers:** Use the `@Headers` annotation to define static headers for a specific method.

```java
@Headers("Cache-Control: max-age=640000")
@GET("widget/list")
Call<List<Widget>> widgetList();
```
- **Dynamic headers:** Use the `@Header` annotation with a corresponding parameter to set headers dynamically. You can also use a `Map<String, String>` with `@HeaderMap` for complex scenarios.

```java
@Headers({
    "Accept: application/vnd.github.v3.full+json",
    "User-Agent: Retrofit-Sample-App"
})
@GET("users/{username}")
Call<User> getUser(@Path("username") String username);
```
Headers that need to be added to every request can be specified using an [OkHttp interceptor](https://square.github.io/okhttp/features/interceptors/).

## Synchronous vs. Asynchronous Calls

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
