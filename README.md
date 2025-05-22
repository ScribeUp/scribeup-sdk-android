# ScribeUpSDK Android

## Installation

The SDK is published to Maven Central. In your app’s `build.gradle`:

```gradle
repositories {
    mavenCentral()
}

dependencies {
    implementation("io.scribeup:scribeupsdk:0.1.0")
}
```

Latest Version
```
0.1.0
```

### Quick Start

1. **Generate an authenticated URL**
   Your backend must call `/v1/auth/users/init` to obtain a time-limited URL (valid for 5 minutes). **Do not** embed API secrets in the client.

   For details on completing authentication and generating a valid URL, please visit [ScribeUp Documentation](https://docs.scribeup.io).

2. **Launch the subscription flow**
   Use `SubscriptionManager` to start the flow and receive a callback when it ends

   ```kotlin
   import io.scribeup.scribeupsdk.SubscriptionManager
   import io.scribeup.scribeupsdk.SubscriptionManagerError
   import io.scribeup.scribeupsdk.SubscriptionManagerListener

   val listener = object : SubscriptionManagerListener {
     override fun onExit(error: SubscriptionManagerError?) {
       if (error == null) {
         Log.e("MyApp", "User exited")
       } else {
         Log.e("MyApp", "Subscription error: ${error.message}")
       }
     }
   }

   SubscriptionManager.present(
     host        = this,
     url         = authenticatedUrl,
     productName = "Subscription Manager",
     listener    = listener
   )
   ```


### API Reference

#### `SubscriptionManager.present`

```kotlin
object SubscriptionManager {
  fun present(
    host: Fragment | FragmentActivity,
    url: String,
    productName: String = "Subscription Manager",
    listener: SubscriptionManagerListener
  )
}
```

- **host**: your `Fragment` or `FragmentActivity`
- **url**: server-generated, short-lived URL for the flow
- **productName**: toolbar title shown in the UI
- **listener**: receives a single `onExit` callback

#### `SubscriptionManagerListener`

```kotlin
interface SubscriptionManagerListener {
  /**
   * Called when the user exits the subscription flow—
   * either by closing it or due to an error.
   *
   * @param error null on normal exit, or a SubscriptionManagerError on failure
   */
  fun onExit(error: SubscriptionManagerError?)
}
```

#### `SubscriptionManagerError`

```kotlin
data class SubscriptionManagerError(
  val code: Int,
  val message: String
)
```

## Author

[ScribeUp](https://scribeup.io)

## License
ScribeUpSDK is released under the MIT license. See the LICENSE file for details.
