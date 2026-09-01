# Tutorial 1: Understanding Fragments - The "Sub-Screen" Component

## What is a Fragment?

Think of an Activity as a **whole page** in your app, and a Fragment as a **reusable section** of that page.

| Concept | Web Analogy | Android Analogy |
|---------|-------------|-----------------|
| Whole page | `index.html` | **Activity** |
| Reusable section | `<div>` or `<iframe>` | **Fragment** |
| Switching sections | JavaScript hiding/showing divs | FragmentManager swapping Fragments |

**Why use Fragments?**
- **Reusability**: You can use the same Fragment in multiple Activities
- **Modularity**: Break complex screens into manageable pieces
- **Flexibility**: Easily swap content in and out without restarting the whole screen

---

## Your First Fragment: A Simple Example

Let's build a simple app with:
- One MainActivity (the container)
- Two Fragments (Screen A and Screen B)
- Two buttons to swap between them

### Step 1: Create the Fragment Layouts

**`res/layout/fragment_screen_a.xml`**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:background="#E3F2FD">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Screen A"
        android:textSize="32sp"
        android:textStyle="bold"
        android:textColor="#1565C0" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="This is Fragment A"
        android:textSize="18sp"
        android:layout_marginTop="16dp" />

</LinearLayout>
```

**`res/layout/fragment_screen_b.xml`**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:background="#FFF3E0">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Screen B"
        android:textSize="32sp"
        android:textStyle="bold"
        android:textColor="#E65100" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="This is Fragment B"
        android:textSize="18sp"
        android:layout_marginTop="16dp" />

</LinearLayout>
```

---

### Step 2: Create the Fragment Classes

**`ScreenAFragment.kt`** (Notice how similar this is to an Activity!)
```kotlin
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class ScreenAFragment : Fragment() {

    // This replaces onCreate() from Activities
    // onCreateView is where we inflate (load) the XML layout
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        // Just like setContentView(R.layout.activity_main), but for Fragments
        return inflater.inflate(R.layout.fragment_screen_a, container, false)
    }

    // This runs AFTER the view is created (like onCreate, but for Fragments)
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // You can use findViewById here just like in an Activity!
        // val myButton = view.findViewById<Button>(R.id.my_button)
    }
}
```

**`ScreenBFragment.kt`**
```kotlin
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class ScreenBFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_screen_b, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // findViewById works here too!
    }
}
```

---

### Step 3: Create the Main Activity Layout

**`res/layout/activity_main.xml`**
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <!-- The CONTAINER where Fragments will be placed -->
    <!-- Think of this as the <div> that will hold your iFrames -->
    <FrameLayout
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:background="#F5F5F5" />

    <!-- Two buttons to swap Fragments -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center"
        android:padding="16dp">

        <Button
            android:id="@+id/btn_screen_a"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Go to Screen A"
            android:layout_marginEnd="8dp" />

        <Button
            android:id="@+id/btn_screen_b"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Go to Screen B"
            android:layout_marginStart="8dp" />

    </LinearLayout>

</LinearLayout>
```

---

### Step 4: The Main Activity - The "Container"

**`MainActivity.kt`**
```kotlin
import android.os.Bundle
import android.widget.Button
import androidx.appcompat.app.AppCompatActivity
import androidx.fragment.app.Fragment

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Load Screen A by default when app opens
        if (savedInstanceState == null) {
            loadFragment(ScreenAFragment())
        }

        // Find the buttons
        val btnA = findViewById<Button>(R.id.btn_screen_a)
        val btnB = findViewById<Button>(R.id.btn_screen_b)

        // Set click listeners to swap Fragments
        btnA.setOnClickListener {
            loadFragment(ScreenAFragment())
        }

        btnB.setOnClickListener {
            loadFragment(ScreenBFragment())
        }
    }

    // Helper function to swap Fragments in the FrameLayout container
    private fun loadFragment(fragment: Fragment) {
        supportFragmentManager.beginTransaction()
            .replace(R.id.fragment_container, fragment)
            .commit()
    }
}
```

---

## Key Takeaways from Tutorial 1

| Activity Concept | Fragment Equivalent | What it does |
|------------------|---------------------|--------------|
| `onCreate()` | `onCreateView()` | Inflates (loads) the XML layout |
| `setContentView(R.layout...)` | `return inflater.inflate(...)` | Returns the view to display |
| `findViewById()` inside Activity | `view.findViewById()` inside Fragment | Finds views in the Fragment's layout |
| `this` (Activity context) | `requireContext()` or `requireActivity()` | Gets the Context |

**Important**: Fragments need a **host Activity** to live in. They cannot exist on their own!

---
