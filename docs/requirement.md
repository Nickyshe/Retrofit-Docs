Retrofit requires at minimum Java 8+ or Android API 21+.

**R8 / PROGUARD**

If you are using R8 the shrinking and obfuscation rules are included automatically.

ProGuard users must manually add the options from [**retrofit2.pro.**](https://github.com/square/retrofit/blob/trunk/retrofit/src/main/resources/META-INF/proguard/retrofit2.pro)

You might also need rules for [**OkHttp**](https://square.github.io/okhttp/features/r8_proguard/) and [**Okio**](https://github.com/square/okio#r8--proguard) which are dependencies of this library.




