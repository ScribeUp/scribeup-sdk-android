# ScribeUp SDK for Android

A simple and powerful SDK for managing subscriptions in your Android app.

## Getting Started

### Installation

1. Add the ScribeUp SDK dependency to your app's `build.gradle` file:

```gradle
dependencies {
    implementation 'io.scribeup:scribeupsdk:0.10.0'
}
```

2. Sync your project with Gradle files.

### Quick Start

1. **Generate an authenticated URL**

   Your backend must call `/v1/auth/users/init` to obtain a time-limited URL (valid for 5 minutes).
   **Do not embed API secrets in the client.**

   For details on completing authentication and generating a valid URL, please visit the [ScribeUp Documentation](https://docs.scribeup.io).

2. **Launch the subscription flow**

   Use `SubscriptionManager` to start the flow and receive callbacks:

```kotlin
import io.scribeup.scribeupsdk.SubscriptionManager
import io.scribeup.scribeupsdk.SubscriptionManagerListener
import io.scribeup.scribeupsdk.data.models.SubscriptionManagerError
import org.json.JSONObject

val listener = object : SubscriptionManagerListener {
  override fun onExit(error: SubscriptionManagerError?, data: Map<String, Any?>?) {
    val errorDescription = if (error != null) {
        "code: ${error.code}, message: ${error.message}"
    } else {
        "No error"
    }

    val dataDescription = try {
        if (data != null) JSONObject(data).toString(2) else "No data"
    } catch (e: Exception) {
        "Failed to serialize data"
    }

    Log.d("ScribeUpSDK", "onExit triggered - $errorDescription, data: $dataDescription")
  }

  override fun onEvent(data: Map<String, Any?>) {
    val dataDescription = try {
        JSONObject(data).toString(2)
    } catch (e: Exception) {
        data.toString()
    }
    Log.d("ScribeUpSDK", "onEvent triggered - data: $dataDescription")
  }
}

SubscriptionManager.present(
  host        = this,
  url         = authenticatedUrl,
  productName = "Subscription Manager",
  listener    = listener
)
```

#### From an Activity

```kotlin
SubscriptionManager.present(
    host = this,
    url = "https://your-subscription-url.com"
)
```

#### From a Fragment

```kotlin
class MyFragment : Fragment(R.layout.fragment_my) {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        SubscriptionManager.present(
            host = this,
            url = "https://your-subscription-url.com"
        )
    }
}
```

> **Important:** Your `Activity` layout must include a `FragmentContainerView` (or similar container) with a valid ID to hold your fragment. The SDK replaces the host fragment using `parentFragmentManager` and `host.id`.

Example layout:

```xml
<!-- activity_main.xml -->
<androidx.fragment.app.FragmentContainerView
    android:id="@+id/fragmentContainer"
    android:name="com.example.MyFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### Widget View Alternative

For more flexible integration scenarios, you can use `SubscriptionManagerWidgetView` - a lightweight View component that can be embedded anywhere in your app and sized however you want.

Unlike the full `SubscriptionManager.present()` API which shows a full-screen modal, the widget view:
- Takes only one parameter: `url`
- Has no header or navigation controls
- Is a simple View that can be sized flexibly
- Is focused purely on displaying web content
- Automatically initializes the SDK when created

```kotlin
// Create the widget view
val widgetView = SubscriptionManagerWidgetView(
    context = this,
    url = "https://your-subscription-url.com"
)

// Size it however you want
widgetView.layoutParams = ViewGroup.LayoutParams(
    ViewGroup.LayoutParams.MATCH_PARENT,
    800 // Or any height you prefer
)

// Add it to your layout
parentContainer.addView(widgetView)
```

You can also add it to XML layouts by setting a placeholder and replacing it programmatically:

```xml
<FrameLayout
    android:id="@+id/subscription_widget_container"
    android:layout_width="match_parent"
    android:layout_height="400dp" />
```

```kotlin
val container = findViewById<FrameLayout>(R.id.subscription_widget_container)
val widgetView = SubscriptionManagerWidgetView(this, url = "https://your-url.com")
container.addView(widgetView)
```

---
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

* **host**: your `FragmentActivity` or `Fragment`

  * If a `Fragment`, the SDK replaces it in its **parent container** using `parentFragmentManager`.
* **url**: server-generated, short-lived URL for the flow
* **productName**: optional title shown in the UI toolbar
* **listener**: receives `onExit` and `onEvent` callbacks

---

#### `SubscriptionManagerListener`

```kotlin
interface SubscriptionManagerListener {
  /**
   * Called when the user exits the experience—
   * either by closing it or due to an error.
   *
   * @param error Error details if the flow ended with a failure; `null` on normal exit.
   * @param data Optional payload returned by the web content; may be `null`.
   */
  fun onExit(error: SubscriptionManagerError?, data: Map<String, Any?>?) {}

  /**
   * Receives event payloads from the web content.
   * Always invoked with a non-null JSON object represented as Map<String, Any>.
   *
   * @param data The event payload.
   */
  fun onEvent(data: Map<String, Any?>) {}
}
```

---
#### `SubscriptionManagerError`

```kotlin
data class SubscriptionManagerError(
  val code: Int,
  val message: String
)
```

---
## Author

[ScribeUp](https://scribeup.io)

## License
ScribeUpSDK is released under the MIT license. See the LICENSE file for details.
