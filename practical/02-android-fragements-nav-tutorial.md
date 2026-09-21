# Tutorial 2: Building a Bottom Tab Navigation Bar with Fragments

## A Detailed Step-by-Step Workbook

---

## Preface: What We Are Building and Why

Before we touch a single line of code, it is worth understanding what we are about to build and why the approach we are taking is considered the professional standard in Android development.

Most beginner Android tutorials teach students to build apps using multiple Activities. You create a `SplashActivity`, a `HomeActivity`, a `DetailsActivity`, and so on. For a three-screen app, this works fine. But the moment you try to build an app with a persistent navigation bar at the bottom — the kind you see in Instagram, WhatsApp, YouTube, or Spotify — the multi-Activity approach starts to fall apart. Every new Activity needs its own copy of the navigation bar XML, its own copy of the click listeners, and its own copy of the logic that decides which tab is highlighted. If you have five tabs, you are maintaining five nearly identical files. Change the colour of the navigation bar, and you have to edit five files. Add a new tab, and you have to edit five files. Forget one, and now your app behaves inconsistently.

The approach we are going to learn uses a single Activity that acts as a **container** or **shell**. Inside that shell lives a navigation bar that never moves, and a blank rectangle where the content changes. When the user taps a different tab, we do not launch a new Activity. Instead, we swap out the content inside that blank rectangle. The navigation bar stays exactly where it is, untouched, unredrawn, and flicker-free.

That blank rectangle will be filled with **Fragments**. A Fragment is best understood as a *sub-screen* or *mini-Activity* that lives inside a host Activity. It has its own layout XML, its own lifecycle, and its own Kotlin class — but it does not exist on its own. It must be hosted inside an Activity.

If you have ever built a website using an `<iframe>`, this analogy will help. The Activity is your main HTML page. The Fragment is the iframe. You can swap the iframe's content without reloading the whole page, and the surrounding chrome (your header, your footer, your navigation) stays perfectly still. This is exactly the relationship between an Activity and a Fragment.

By the end of this tutorial, you will have a working app with three tabs — Home, Search, and Profile — each showing a distinct screen, all controlled by a single Activity.

Let us begin.

---

## Project Setup: Before We Begin

### What You Should Have Before Starting

Open Android Studio and create a new project using the **Empty Views Activity** template. This is important: choose *Empty Views Activity*, not *Empty Activity*, because the latter defaults to Jetpack Compose, which we are deliberately avoiding in this tutorial.

Use the following settings:

- **Project Name:** `BottomNavApp`
- **Package Name:** `com.yourname.bottomnavapp`
- **Minimum SDK:** API 24 (Android 7.0 Nougat) — this covers roughly 97 percent of active Android devices while still allowing modern APIs
- **Build Configuration Language:** Kotlin DSL (recommended) or Groovy
- **Language:** Kotlin

### What Your Project Should Look Like Immediately After Creation

When the project finishes building and indexing, your Project pane in Android Studio should show the following structure. If you are viewing in *Android* mode (the default), you will see a logical grouping. If you switch to *Project* mode, you will see the raw file system. Either is fine — we will use the Android view throughout this tutorial for clarity.

```
app/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/yourname/bottomnavapp/
│       │       └── MainActivity.kt          <- Your only Activity
│       ├── res/
│       │   ├── layout/
│       │   │   └── activity_main.xml        <- Your main layout
│       │   ├── values/
│       │   │   ├── colors.xml
│       │   │   ├── strings.xml
│       │   │   └── themes.xml
│       │   └── drawable/                    <- Currently empty (or has default icons)
│       └── AndroidManifest.xml
```

Spend a moment locating `MainActivity.kt` and `activity_main.xml`. These are the two files we will be modifying most heavily. Open them both so you can see their default contents. `MainActivity.kt` will contain a stub `onCreate` method that calls `setContentView(R.layout.activity_main)`. The layout file will contain a single `TextView` displaying "Hello World!".

We are about to replace most of that.

---

## Step 1: Add the Material Design Dependency

### Context: Why We Need This

Android's UI toolkit comes in layers. The core Android SDK gives you basic widgets like `Button`, `TextView`, and `LinearLayout`. On top of that sits **AndroidX**, a collection of libraries that backport newer features to older devices and provide modern replacements for legacy widgets. And on top of *that* sits the **Material Components library**, which implements Google's Material Design specification — the design language that gives Android apps their characteristic look with elevated cards, floating action buttons, and yes, bottom navigation bars.

The `BottomNavigationView` widget we are about to use does not ship with the core Android SDK. It lives in the Material Components library. If we do not add that library to our project, Android Studio will not recognise the widget name in our XML, and the app will fail to compile.

This is a one-time setup step. Once the dependency is added, it stays available for the rest of the project.

### What To Do

Open the file `app/build.gradle.kts` (if you chose Kotlin DSL) or `app/build.gradle` (if you chose Groovy). Do not confuse this with the project-level `build.gradle` file — you want the one inside the `app/` folder, which is where module dependencies are declared.

Locate the `dependencies { ... }` block. It will already contain several `implementation` lines for core AndroidX libraries. Add the following line among them:

```gradle
implementation 'com.google.android.material:material:1.11.0'
```

Your dependencies block should now resemble this (versions may differ slightly depending on your Android Studio version — that is fine):

```gradle
dependencies {
    implementation 'androidx.core:core-ktx:1.12.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.11.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.5'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.5.1'
}
```

After saving the file, a yellow banner will appear at the top of the editor reading something like *"Gradle files have changed since last project sync. A project sync may be necessary for the IDE to work properly."* Click **Sync Now**.

### What You Should See After

The sync process will run for ten to thirty seconds. Watch the status bar at the bottom of Android Studio — it will show *"Gradle Build Running"* and then *"Gradle Sync Finished"*. Once complete, the yellow banner disappears. If you now begin typing `<com.google.android.material.` inside any XML file, Android Studio will offer autocomplete suggestions, which confirms the library is available.

### A Note on Version Numbers

The version `1.11.0` is current at the time of writing. Material Components releases new versions periodically. If you see a warning that a newer version is available, you may safely update the number, but for this tutorial the specified version will work identically.

---

## Step 2: Download and Import Icons

### Context: Why We Need Icons and Where They Come From

A bottom navigation bar is fundamentally a set of small, recognisable pictures with labels underneath. The pictures are vector icons. Android's preferred icon format is **VectorDrawable** — an XML file that describes shapes mathematically rather than as a grid of pixels. Vector icons scale to any screen density without becoming blurry, and they are tiny compared to PNG images.

You have two options for obtaining icons: use Android Studio's built-in Vector Asset tool (which pulls from Google's Material Icons collection), or download icons from an external source such as **Lucide** (`https://lucide.dev/icons`), a clean open-source icon set that uses the SVG format.

We will cover both methods, because each teaches something valuable. The Android Studio method is faster and guarantees compatibility. The Lucide method teaches you how to convert external assets, which is a skill you will need when a designer hands you a custom icon set.

### Method A: Using Android Studio's Vector Asset Tool (Recommended for Beginners)

In the Project pane, right-click on the `res/drawable` folder and choose **New > Vector Asset**. A dialog appears with two options at the top: *Clip Art* and *Local File*. Leave *Clip Art* selected.

Click the small Android-robot icon next to the *Clip Art* field. A searchable grid of icons appears. Type `home` in the search box and select the icon that looks like a house. Below the icon, you will see options for the asset name (change it to `ic_home`), size (leave at `24dp` by `24dp`), and colour (leave as black — we will tint it later in XML). Click **Next**, then **Finish**.

Repeat the process twice more: search for `search` and name it `ic_search`, then search for `person` and name it `ic_profile`.

### Method B: Downloading from Lucide (For Understanding External Assets)

Open your browser and navigate to `https://lucide.dev/icons`. In the search bar, type `home`. Click on the "Home" result. On the detail page, click the **Copy SVG** button. The SVG code is now on your clipboard.

Open a plain text editor. Paste the SVG. It will look something like this:

```xml
<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
  <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/>
  <polyline points="9 22 9 12 15 12 15 22"/>
</svg>
```

This is an SVG, not an Android VectorDrawable. The two formats are similar in spirit but use different syntax. To convert, you can either paste the SVG into a tool like `svg2android` (there is a well-known online converter), or you can manually rewrite it in Android's format. Here is the Android equivalent of the Lucide home icon:

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24">
    <path
        android:pathData="M3,9 L12,2 L21,9 L21,20 A2,2 0 0,1 19,22 L5,22 A2,2 0 0,1 3,20 Z"
        android:strokeColor="#000000"
        android:strokeWidth="2"
        android:fillColor="#00000000"
        android:strokeLineCap="round"
        android:strokeLineJoin="round" />
    <path
        android:pathData="M9,22 L9,12 L15,12 L15,22"
        android:strokeColor="#000000"
        android:strokeWidth="2"
        android:fillColor="#00000000"
        android:strokeLineCap="round"
        android:strokeLineJoin="round" />
</vector>
```

Save this as `ic_home.xml` inside `res/drawable`. Repeat for the other two icons.

For most students, Method A is strongly recommended. Method B is included so you understand that icons are not magic — they are just XML files describing shapes, and you can produce them yourself or obtain them from any source.

### What You Should See After

Your `res/drawable` folder contains three new files:

```
res/drawable/
├── ic_home.xml
├── ic_search.xml
└── ic_profile.xml
```

If you open any of them, Android Studio displays a small preview of the icon on the right side of the editor. The preview for `ic_home.xml` should show a house outline.

---

## Step 3: Create the Menu XML

### Context: What a Menu Resource Is and Why We Need One

Android separates the *definition* of a menu (what items exist, what icons they use, what labels they show) from the *presentation* of that menu (how it appears on screen). The definition lives in an XML file inside `res/menu/`. The `BottomNavigationView` then references that file with a single attribute: `app:menu="@menu/bottom_nav_menu"`.

This separation matters. It means you can redesign the entire look of your navigation bar in the layout XML without touching the menu definition, or restructure the menu without touching the layout. It also means the menu can be reused by other widgets — for example, an overflow menu in the toolbar can reference the same items.

The `res/menu/` directory does not exist by default in a new project. We must create it.

### What To Do

Right-click on the `res` folder in the Project pane and choose **New > Android Resource Directory**. In the dialog, set *Directory name* to `menu` and *Resource type* to `menu`. Click **OK**. A new empty folder appears.

Now right-click on `res/menu` and choose **New > Menu Resource File**. Name the file `bottom_nav_menu` and click **OK**. Android Studio creates the file with a basic `<menu>` root element.

Replace the entire contents with the following:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/navigation_home"
        android:icon="@drawable/ic_home"
        android:title="Home" />

    <item
        android:id="@+id/navigation_search"
        android:icon="@drawable/ic_search"
        android:title="Search" />

    <item
        android:id="@+id/navigation_profile"
        android:icon="@drawable/ic_profile"
        android:title="Profile" />

</menu>
```

### Deep Dive: What Each Attribute Means

The `<item>` element defines a single tab. The `android:id` attribute gives the tab a unique identifier that our Kotlin code will use to detect which tab was tapped. The `@+id/` syntax tells Android's resource compiler to create a new ID entry in the auto-generated `R` class. This is the same mechanism you have used for naming buttons and text views.

The `android:icon` attribute points to a drawable resource. The `@drawable/ic_home` syntax means *"look in the drawable folder for a resource named `ic_home`"*. If the file is missing or misnamed, the resource compiler will produce an error at build time — not at runtime — which is exactly what we want. Catching a missing icon during compilation is far preferable to discovering it when a user opens the app.

The `android:title` attribute provides the label displayed beneath the icon. This is a literal string, but in a production app you would typically place it in `strings.xml` and reference it as `@string/nav_home`. This makes localisation into other languages possible without editing XML layouts. For the purposes of this tutorial, the literal strings keep the file readable, but you should know the professional convention.

### What You Should See After

The file `bottom_nav_menu.xml` exists in `res/menu/`. If you switch the editor to *Design* view (the tab at the bottom-right of the XML editor), Android Studio shows a preview of the three items in a mock navigation bar. There should be no red underlines — every `@drawable/` reference resolves successfully.

---

## Step 4: Create the Three Fragment Layouts

### Context: Why Each Fragment Needs Its Own Layout

Remember our analogy: a Fragment is like an iframe. Just as an iframe points to its own HTML document, a Fragment points to its own layout XML. Each Fragment layout describes what that particular screen looks like. The Home tab might show a feed. The Search tab might show a search field. The Profile tab might show a user's avatar and details.

We will create three separate layout files, each with a distinctive appearance so that when you tap between tabs, you can *see* the content change. The colours and layouts we choose are deliberately simple — this is not a design tutorial — but they are distinct enough to make the swap visually obvious.

Notice that none of these layouts contain a navigation bar. That is the entire point of this architecture. The navigation bar lives in `activity_main.xml` and never moves. Only the content inside the container changes.

### What To Do: Create `fragment_home.xml`

Right-click on `res/layout` and choose **New > Layout Resource File**. Set the file name to `fragment_home`, leave the root element as `LinearLayout`, and click **OK**.

Replace the contents with:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp"
    android:background="#E8F5E9">

    <ImageView
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:src="@drawable/ic_home"
        android:tint="#2E7D32" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Home"
        android:textSize="28sp"
        android:textStyle="bold"
        android:textColor="#2E7D32"
        android:layout_marginTop="16dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Welcome to your Home tab."
        android:textSize="16sp"
        android:textColor="#555555"
        android:layout_marginTop="8dp" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="This is where your feed would appear."
        android:textSize="14sp"
        android:textColor="#888888"
        android:gravity="center"
        android:layout_marginTop="32dp"
        android:padding="16dp"
        android:background="#FFFFFF" />

</LinearLayout>
```

### What To Do: Create `fragment_search.xml`

Right-click on `res/layout` again, choose **New > Layout Resource File**, name it `fragment_search`, root element `LinearLayout`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp"
    android:background="#FFF8E1">

    <ImageView
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:src="@drawable/ic_search"
        android:tint="#F57F17" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Search"
        android:textSize="28sp"
        android:textStyle="bold"
        android:textColor="#F57F17"
        android:layout_marginTop="16dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Find what you are looking for."
        android:textSize="16sp"
        android:textColor="#555555"
        android:layout_marginTop="8dp" />

    <View
        android:layout_width="match_parent"
        android:layout_height="48dp"
        android:background="#FFFFFF"
        android:layout_marginTop="32dp" />

</LinearLayout>
```

### What To Do: Create `fragment_profile.xml`

Right-click on `res/layout` once more, name it `fragment_profile`, root element `LinearLayout`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="24dp"
    android:background="#E3F2FD">

    <ImageView
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:src="@drawable/ic_profile"
        android:tint="#0D47A1" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Profile"
        android:textSize="28sp"
        android:textStyle="bold"
        android:textColor="#0D47A1"
        android:layout_marginTop="16dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Your account information."
        android:textSize="16sp"
        android:textColor="#555555"
        android:layout_marginTop="8dp" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Name: John Doe"
        android:textSize="14sp"
        android:padding="12dp"
        android:layout_marginTop="32dp"
        android:background="#FFFFFF" />

</LinearLayout>
```

### Deep Dive: Why `gravity="center"` and How `LinearLayout` Works

A `LinearLayout` arranges its children either horizontally or vertically depending on its `android:orientation` attribute. We set it to `vertical`, so each child sits below the previous one, stacked like paragraphs in a document.

By default, a vertical `LinearLayout` places its children at the top and aligns them to the left. The `android:gravity="center"` attribute changes this: it tells the layout to centre *its children* both horizontally and vertically within the layout's bounds. This is what gives our content the pleasant "floating in the middle" appearance rather than being crammed into the top-left corner.

Be careful not to confuse `android:gravity` with `android:layout_gravity`. The first controls how a layout positions its children; the second controls how a view positions itself within its parent. This is a common source of confusion for beginners, and the distinction will matter later when you design more complex screens.

The `android:background` attribute sets a solid colour. We chose light, pastel colours so the three screens look clearly different when you switch between them. This is not decorative — it is diagnostic. If the background colour does not change when you tap a tab, something is wrong.

### What You Should See After

Three new files in `res/layout`:

```
res/layout/
├── activity_main.xml        (default, will be replaced in Step 7)
├── fragment_home.xml        (green screen)
├── fragment_search.xml      (yellow screen)
└── fragment_profile.xml     (blue screen)
```

Switch the editor to *Design* view to preview each one. You should see a centred icon, a large title, and a smaller subtitle on each.

---

## Step 5: Create a Circle Background Drawable (Optional)

### Context: How to Draw Shapes Without Images

Sometimes the visual element you need is not an icon but a shape — a circle, a rounded rectangle, a gradient. Android lets you describe these shapes in XML using a `<shape>` drawable, avoiding the need to import an image entirely. This is a small but elegant technique that reinforces the idea that Android resources are just XML definitions of visual properties.

We will create a circular background that could frame the profile icon in a real app. This step is optional; if you skip it, your profile screen will still work perfectly. But it teaches a useful technique.

### What To Do

Right-click on `res/drawable` and choose **New > Drawable Resource File**. Name the file `circle_background`. Change the *Root element* from `vector` to `shape`, and click **OK**.

Replace the contents with:

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">

    <solid android:color="#E3F2FD" />
    <stroke
        android:width="2dp"
        android:color="#0D47A1" />

</shape>
```

To use it, open `fragment_profile.xml` and add `android:background="@drawable/circle_background"` to the `ImageView`. The image will now appear inside a blue circle.

### Deep Dive: What This Drawable Does

The `<solid>` element fills the shape with a colour. The `<stroke>` element draws an outline around it. Because `android:shape` is set to `oval`, the rectangle defined by the view's bounds becomes an ellipse — and since our ImageView is 100dp by 100dp, a perfect circle.

This is a small example of a broader principle: in Android, you rarely need to import pixel-perfect images for simple decorative elements. Shapes, gradients, and even complex layered compositions can all be described in XML.

---

## Step 6: Create the Three Fragment Classes

### Context: Why a Fragment Needs Both a Layout and a Class

A layout describes *what the screen looks like*. A class describes *what the screen does*. They are separate because in a well-designed app, the same layout might be reused with different behaviour, or the same behaviour might be applied to different layouts. The Fragment class is the glue that connects a layout to logic.

If you have written Activities before, the Fragment class will feel familiar — but with important differences in the lifecycle. Where an Activity overrides `onCreate` and calls `setContentView`, a Fragment overrides `onCreateView` and *returns* the inflated view. This is because a Fragment does not own itself; it is attached to a host Activity, and the host decides when the Fragment's view should be created and destroyed. Returning the view rather than setting it is a subtle but crucial distinction.

### What To Do: Create `HomeFragment.kt`

In the Project pane, right-click on the package folder `com.yourname.bottomnavapp` and choose **New > Kotlin Class/File**. Name it `HomeFragment` and choose *Class* as the kind.

Replace the contents with:

```kotlin
package com.yourname.bottomnavapp

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class HomeFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_home, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // Any post-inflation logic goes here.
        // For example:
        // view.findViewById<Button>(R.id.myButton).setOnClickListener { ... }
    }
}
```

### What To Do: Create `SearchFragment.kt`

Same procedure, name it `SearchFragment`.

```kotlin
package com.yourname.bottomnavapp

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class SearchFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_search, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // Search-specific logic goes here.
    }
}
```

### What To Do: Create `ProfileFragment.kt`

Same procedure, name it `ProfileFragment`.

```kotlin
package com.yourname.bottomnavapp

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

class ProfileFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_profile, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // Profile-specific logic goes here.
    }
}
```

### Deep Dive: The `onCreateView` Signature Explained

The method signature is verbose but every parameter has a purpose.

The `inflater: LayoutInflater` parameter is a tool that turns XML into actual View objects. You do not create it yourself; the Android framework provides it. You simply call `inflater.inflate(...)` and pass in the layout resource ID.

The `container: ViewGroup?` parameter is the parent view that will hold this Fragment's view — in our case, the `FrameLayout` inside `activity_main.xml`. It is nullable because a Fragment *can* exist without a container in rare cases (for example, a Fragment with no UI), but in our case it will always be non-null.

The `savedInstanceState: Bundle?` parameter is a bundle of previously saved state, provided when Android recreates a Fragment after a configuration change such as screen rotation. It is null the first time the Fragment is created. We do not use it in this tutorial, but you should know it exists.

The `return` statement is the critical line: `inflater.inflate(R.layout.fragment_home, container, false)` loads the XML layout and returns the resulting `View` object back to the framework, which then places it inside the container. The third argument, `false`, tells the inflater *not* to immediately attach the view to the container. If you pass `true`, Android will throw a runtime exception. This is one of the most common mistakes beginners make.

### What You Should See After

Three new Kotlin files in your package:

```
java/com/yourname/bottomnavapp/
├── MainActivity.kt
├── HomeFragment.kt
├── SearchFragment.kt
└── ProfileFragment.kt
```

None of them should show red underlines if the imports are correct. If you see an error like *"Unresolved reference: fragment_home"*, it means the layout file from Step 4 is missing or misnamed.

---

## Step 7: Design the Main Activity Layout

### Context: The Container and the Navigation Bar

This is the file that ties everything together. It contains exactly two children: a `FrameLayout` that occupies all available space above the navigation bar, and a `BottomNavigationView` that sits at the bottom and never moves.

The `FrameLayout` is deliberately empty. It has no content of its own. It is a placeholder — a hole in the layout that our Kotlin code will fill with Fragments. The `FrameLayout` is the single most important container in this architecture because it is the location where content swaps occur.

The `BottomNavigationView` is a specialised widget from the Material library. It reads its items from the menu XML we created in Step 3 and renders them as tappable tabs. It handles its own visual state — highlighting the selected tab, animating transitions, and dispatching callbacks when the user taps a different item.

### What To Do

Open `res/layout/activity_main.xml` and replace the entire contents with:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toTopOf="@id/bottom_navigation" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_navigation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:menu="@menu/bottom_nav_menu"
        app:layout_constraintBottom_toBottomOf="parent"
        android:background="?attr/colorSurface"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### Handling the Colour Reference

You will likely see a red underline on `@color/bottom_nav_color` because that colour does not exist yet. Open `res/values/colors.xml` and add:

```xml
<color name="bottom_nav_color">#6200EE</color>
```

Save the file. The red underline disappears.

### Deep Dive: How ConstraintLayout Positions These Two Views

`ConstraintLayout` is a layout in which every child view must declare how it is positioned relative to other views or to the parent. There is no implicit positioning. This is why it is powerful — but also why it demands that you be explicit.

Our `FrameLayout` declares two constraints: `app:layout_constraintTop_toTopOf="parent"` pins its top edge to the top of the parent, and `app:layout_constraintBottom_toTopOf="@id/bottom_navigation"` pins its bottom edge to the top of the navigation bar. Its height is set to `0dp`, which in `ConstraintLayout` means *"fill the space between my top and bottom constraints"*. The result: the `FrameLayout` occupies everything above the navigation bar.

Our `BottomNavigationView` declares one constraint: `app:layout_constraintBottom_toBottomOf="parent"`, pinning it to the bottom of the screen. Its height is `wrap_content`, meaning it takes only as much vertical space as it needs. Because it is declared *after* the `FrameLayout` in the XML, it draws on top — though this does not matter here, since their bounds do not overlap.

The diagram below shows the resulting layout:

```
+-----------------------------------+
|                                    |
|                                    |
|                                    |
|        FrameLayout                 |  <- Empty, waiting for a Fragment
|        (fragment_container)        |
|                                    |
|                                    |
|                                    |
+-----------------------------------+
|  [⌂ Home]  [⌕ Search]  [◉ Profile] |  <- BottomNavigationView
+-----------------------------------+
```

The three symbols (⌂, ⌕, ◉) represent the home, search, and profile icons respectively.

### What You Should See After

Switching to *Design* view in `activity_main.xml` should show a large empty area at the top and a coloured bar at the bottom with three icons. If the icons do not appear, one of the drawable references in `bottom_nav_menu.xml` is broken — return to Step 3 and verify the file names.

---

## Step 8: Write the Main Activity Code

### Context: What the Activity Does and Does Not Do

The `MainActivity` has exactly three responsibilities. It loads the default Fragment when the app first opens. It listens for tab taps on the navigation bar. And when a tab is tapped, it swaps the Fragment inside the `FrameLayout` container.

Notice what the Activity does *not* do. It does not contain any of the content shown on the Home, Search, or Profile screens. That content lives inside the individual Fragments. The Activity is a coordinator, not a content owner. This separation is what allows the same Activity to host many different screens without becoming bloated.

### What To Do

Open `MainActivity.kt` and replace the entire contents with:

```kotlin
package com.yourname.bottomnavapp

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.fragment.app.Fragment
import com.google.android.material.bottomnavigation.BottomNavigationView

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val bottomNavigationView =
            findViewById<BottomNavigationView>(R.id.bottom_navigation)

        if (savedInstanceState == null) {
            loadFragment(HomeFragment())
        }

        bottomNavigationView.setOnItemSelectedListener { menuItem ->
            when (menuItem.itemId) {
                R.id.navigation_home -> {
                    loadFragment(HomeFragment())
                    true
                }
                R.id.navigation_search -> {
                    loadFragment(SearchFragment())
                    true
                }
                R.id.navigation_profile -> {
                    loadFragment(ProfileFragment())
                    true
                }
                else -> false
            }
        }
    }

    private fun loadFragment(fragment: Fragment) {
        supportFragmentManager
            .beginTransaction()
            .replace(R.id.fragment_container, fragment)
            .commit()
    }
}
```

### Deep Dive: Line-by-Line Explanation

The line `val bottomNavigationView = findViewById<BottomNavigationView>(R.id.bottom_navigation)` looks up the navigation bar we defined in XML and stores a reference to it. The angle brackets `<BottomNavigationView>` tell Kotlin what type of object to expect, avoiding the need for a cast.

The block beginning `if (savedInstanceState == null)` loads the Home Fragment, but only when the Activity is being created for the first time. The `savedInstanceState` bundle is non-null whenever Android is recreating an Activity after a configuration change — most commonly, a screen rotation. In that case, the Fragment is already in the container (Android restores it automatically), and loading another copy would result in *two* Home Fragments stacked on top of each other, which is a subtle and confusing bug. This check is our defence against it.

The call `bottomNavigationView.setOnItemSelectedListener { menuItem -> ... }` registers a lambda that fires whenever the user taps a tab. The `menuItem` parameter tells us which tab was tapped. Inside the `when` expression, we compare `menuItem.itemId` against the three IDs we defined in `bottom_nav_menu.xml`. When a match is found, we load the corresponding Fragment and return `true`, which signals to the framework that the event has been handled.

The `else -> false` branch handles the theoretical case of an unmatched ID — which should never happen in a well-formed menu, but must be present because Kotlin's `when` used as an expression requires exhaustiveness.

The `loadFragment` function performs the actual swap. The `supportFragmentManager` is provided by `AppCompatActivity` and manages all Fragment transactions for the Activity. The `beginTransaction()` call starts a new transaction. The `replace(R.id.fragment_container, fragment)` call removes whatever Fragment is currently in the container and adds the new one. The `commit()` call finalises and executes the transaction.

### Deep Dive: Why `replace` and Not `add`

You may wonder why we use `replace` rather than `add`. If we used `add`, each Fragment would stack on top of the previous one, and we would need to manually remove them later. `replace` removes the existing Fragment and adds the new one in a single, atomic operation. It is simpler and correct for our use case.

There is a performance trade-off: `replace` destroys the outgoing Fragment's view, so any scroll position or input state is lost. If you later need to preserve state between tabs, you would switch to `add` and `hide`, then `show` when returning. That is an advanced topic beyond this tutorial, but you should know the trade-off exists.

### What You Should See After

There should be no red underlines in `MainActivity.kt`. If you see *"Unresolved reference: HomeFragment"*, check that `HomeFragment.kt` is in the same package and that its class declaration is not misspelled.

---

## Step 9: Run the App

### What You Should See When the App Launches

The app opens on the Home tab. The screen is pale green, with a large home icon, the title "Home", and a card-like message reading "This is where your feed would appear." At the bottom, a purple navigation bar shows three items: Home (highlighted), Search, and Profile.

### What You Should See When You Tap Each Tab

Tapping Search changes the content area to a pale yellow screen with the search icon and a placeholder search bar. The navigation bar does not move, does not flicker, and does not reload. The Search tab becomes highlighted, and the Home tab returns to its unselected colour.

Tapping Profile changes the content to a pale blue screen with the profile icon and a placeholder user name.

Tapping Home returns to the green screen.

### What You Should Not See

You should not see a white flash between tabs. You should not see the navigation bar redrawing itself. You should not see the whole screen slide in from the right (that would be an Activity transition). The content should change instantly and only within the area above the navigation bar.

If you observe any of these unwanted behaviours, the troubleshooting section below will help you diagnose them.

### Diagram: The Three States

```
State 1: Home tab selected
+------------------------------------+
|                                    |
|          [⌂]                       |
|         Home                       |
|   Welcome to your Home tab.        |
|                                    |
+------------------------------------+
|  [⌂ Home]  [⌕ Search]  [◉ Profile] |
|  ^^^^^^^^^                         |
+------------------------------------+

State 2: Search tab selected
+------------------------------------+
|                                    |
|          [⌕]                       |
|         Search                     |
|   Find what you are looking for.   |
|                                    |
+------------------------------------+
|  [⌂ Home]  [⌕ Search]  [◉ Profile] |
|            ^^^^^^^^^^              |
+------------------------------------+

State 3: Profile tab selected
+------------------------------------+
|                                    |
|          [◉]                       |
|         Profile                    |
|   Your account information.        |
|                                    |
+------------------------------------+
|  [⌂ Home]  [⌕ Search]  [◉ Profile] |
|                        ^^^^^^^^^^  |
+------------------------------------+
```

The caret markers (`^^^^^`) indicate which tab is highlighted in each state.

---

## Troubleshooting: Expanded Guide

The errors below are grouped by category. Each entry describes what you will see, what it means, and how to fix it. Read this section *before* running into problems — many of these will save you hours of frustration.

### Category 1: Build-Time Errors

**Error: "Unresolved reference: material" or "Cannot resolve symbol BottomNavigationView"**

You will see this as a red underline in your XML or Kotlin file, and the build will fail with a message mentioning an unresolved reference. The cause is almost always that the Material Components dependency is either missing from `build.gradle` or the project has not been synced since you added it. Open `app/build.gradle.kts`, verify the line `implementation 'com.google.android.material:material:1.11.0'` is present inside the `dependencies` block, and click *Sync Now* if a banner appears.

**Error: "Unresolved reference: R"**

This is one of the most common Android build errors, and it is almost never actually about the `R` class itself. The `R` class is generated automatically by Android's resource compiler. It will fail to generate if *any* resource file in your project contains an error. Common culprits include a missing drawable reference in a layout, a mismatched file name (uppercase letters are not allowed), or a syntax error in a menu XML. Open the *Build* tab at the bottom of Android Studio and read the first error listed — the `R` error is often a downstream symptom, and the real cause is earlier in the log.

**Error: "Resource drawable/ic_home not found"**

You referenced `@drawable/ic_home` somewhere, but the file does not exist. Check that the file is exactly named `ic_home.xml` (all lowercase, underscore not hyphen) and lives directly inside `res/drawable/`. If you placed it inside a subfolder, the reference would need to include that subfolder path.

**Error: "Duplicate resources"**

You have two files with the same name and type in the project. For example, if you created `ic_home.xml` twice with the Vector Asset tool using the same name both times. Delete the duplicate, or rename one of them and update its references.

### Category 2: Runtime Errors (App Crashes on Launch)

**Crash: "Binary XML file line #N: Error inflating class com.google.android.material.bottomnavigation.BottomNavigationView"**

This means the Material library was not available at runtime. Usually this occurs when you edited the `build.gradle` file but did not perform a full rebuild — the dependency was added to the configuration but not packaged into the APK. Run **Build > Clean Project**, then **Build > Rebuild Project**, then relaunch.

**Crash: "java.lang.IllegalStateException: Fragment HomeFragment not attached to a context"**

This means the Fragment tried to access the Activity before it was attached. This typically happens if you call `requireContext()` or `requireActivity()` inside `onCreateView` before the view is fully created. Move such calls to `onViewCreated` or later. In our simple tutorial this crash should not occur, but it is worth knowing for future work.

**Crash: "java.lang.IllegalArgumentException: No view found for id 0x7f080001 (com.yourname.bottomnavapp:id/fragment_container)"**

This means your `loadFragment` function tried to place a Fragment into a container that does not exist in the current layout. Check that the `FrameLayout` ID in `activity_main.xml` is spelled exactly `fragment_container`, matching the ID you pass to `replace()` in `MainActivity.kt`. A single typo — for example, `fragment_container` versus `fragment_containter` — will cause this crash.

### Category 3: Logical Errors (App Runs But Behaves Incorrectly)

These are the errors that will not crash your app but will cause it to behave in ways that confuse you. They are the most educational, because diagnosing them requires you to reason about the interaction between your code, your resources, and the Android framework.

**Symptom: Tapping a tab does nothing**

The navigation bar shows the tabs, and tapping them highlights them visually, but the content area does not change. The most likely cause is that your `setOnItemSelectedListener` block is missing, misspelled, or attached to the wrong view. Verify that you call `bottomNavigationView.setOnItemSelectedListener` on the `BottomNavigationView` instance, not on some other view. Also verify that the `when` branches compare against the correct IDs — `R.id.navigation_home`, not, for example, `R.id.nav_home`.

**Symptom: The app crashes when you rotate the screen**

Rotation triggers a configuration change, which causes Android to destroy and recreate the Activity. If your `savedInstanceState == null` check is missing, the Home Fragment will be loaded *again* on top of the restored one, potentially causing a crash or a duplicated UI. Verify that the check is present. This is one of the most important defensive patterns in Android development.

**Symptom: The navigation bar appears but no icons are visible**

The tabs are there and tappable, but the icons are blank or missing. This usually means the drawable files referenced in `bottom_nav_menu.xml` are empty or malformed. Open each drawable file and confirm it contains a valid `<vector>` element. If you copied SVG code directly without converting it to Android's format, the file will not be understood by Android's resource compiler, even though it may look correct in a text editor.

**Symptom: The navigation bar is visible but the content area shows nothing**

The tabs work, but the Home Fragment never appears on launch. Check that `loadFragment(HomeFragment())` is called inside the `if (savedInstanceState == null)` block. A common mistake is to place it *inside* the `setOnItemSelectedListener` block, which means it only fires when a tab is tapped, not when the app launches.

**Symptom: Multiple Fragments appear stacked on top of each other**

You see overlapping text or icons that suggest two screens are drawn in the same space. This typically happens when `replace` is used but the transaction is committed twice — for example, if you accidentally call `loadFragment` inside both `onCreate` and the listener callback for the initially selected tab. Review your `onCreate` method and confirm that only one Fragment is loaded on launch.

**Symptom: Tapping the currently-selected tab reloads the same screen**

The content flickers briefly when you tap the tab you are already on. This is because `setOnItemSelectedListener` fires even when the same tab is tapped again. In a more polished app, you would check whether the incoming Fragment is already displayed and skip the swap. For our tutorial this is acceptable behaviour, but you should know it happens.

**Symptom: The selected tab's highlight does not move**

You tap a different tab, and the content area updates, but the highlighted tab in the navigation bar does not change. This is rare but can occur if the `menuItem.itemId` values in your listener do not exactly match the IDs in `bottom_nav_menu.xml`. Verify them character by character. Also, if you returned `false` from your listener — which tells the framework *"I did not handle this event"* — the highlight will not update. All branches except the `else` should return `true`.

### Category 4: Common Misunderstandings

**Misunderstanding: "Can I use findViewById inside a Fragment?"**

Yes, but not in the same way as in an Activity. In an Activity, `findViewById` searches the entire Activity's view hierarchy. In a Fragment, you must call `view.findViewById(...)` inside `onViewCreated`, where `view` is the Fragment's root view. If you call `findViewById` without the `view.` prefix inside a Fragment, you will get a compilation error, because Fragment does not extend Context the way Activity does.

**Misunderstanding: "Do I need to call setContentView in a Fragment?"**

No. That method belongs to Activity. In a Fragment, you return the inflated view from `onCreateView` instead. The framework takes care of attaching it.

**Misunderstanding: "Why does my app crash if I forget to return the view from onCreateView?"**

Because the framework needs the view to display it. If `onCreateView` returns null, the Fragment is treated as having no UI, and any attempt to interact with it will fail. Always return the inflated view unless you are deliberately creating a headless Fragment (an advanced topic).

**Misunderstanding: "Can a Fragment exist without an Activity?"**

No. A Fragment must always be attached to a host Activity. If you try to use a Fragment outside an Activity context, you will get a crash. This is why `requireActivity()` exists — it asserts that an Activity is available and crashes with a clear message if not.

---

## Review Questions

These questions are designed to test not just memory but understanding. Try to answer each in a sentence or two before moving on.

1. Why do we use a single Activity with multiple Fragments, rather than multiple Activities with duplicated navigation bars?

2. What is the role of the `FrameLayout` with ID `fragment_container` in `activity_main.xml`?

3. What is the difference between `onCreate` in an Activity and `onCreateView` in a Fragment?

4. Why do we check `if (savedInstanceState == null)` before loading the initial Fragment?

5. What does `supportFragmentManager.beginTransaction().replace(...).commit()` actually do?

6. Why is the `container` parameter passed to `inflater.inflate` important, and why must the third argument be `false`?

7. What would happen if you used `add()` instead of `replace()` in the `loadFragment` function?

8. Why does the navigation bar not flicker when you switch tabs, whereas a multi-Activity design would?

---

## Summary Checklist

Before considering this tutorial complete, verify each item:

- [ ] Material Components dependency added to `app/build.gradle.kts`
- [ ] Three icon drawables created in `res/drawable/`
- [ ] `bottom_nav_menu.xml` created in `res/menu/` with three items
- [ ] Three Fragment layouts created in `res/layout/`
- [ ] Three Fragment classes created in the package folder
- [ ] `activity_main.xml` redesigned with `FrameLayout` and `BottomNavigationView`
- [ ] `MainActivity.kt` rewritten with `onCreate`, listener, and `loadFragment` helper
- [ ] App launches without crashing
- [ ] Tapping each tab changes the content area
- [ ] The navigation bar remains stationary during swaps
- [ ] Rotating the device does not crash the app or duplicate content

---

## Extension Exercises

Once the base tutorial is working, consider these extensions. Each builds directly on what you have learned.

1. **Add a fourth tab.** Add a "Settings" tab with a gear icon. This reinforces that adding tabs is a matter of adding one entry to the menu XML, one layout, one Fragment class, and one branch in the `when` expression.

2. **Make the Search tab functional.** Add a `Toast` message that appears when the Search screen is opened. This introduces the idea that Fragments can respond to their own lifecycle events.

3. **Change the highlighted tab colour.** Modify `bottom_nav_color` and observe how the tint applies to both icons and labels. This teaches you that Material widgets respect design tokens defined in resources.

4. **Use ViewBinding instead of findViewById.** ViewBinding is a compile-time-safe alternative to `findViewById` that eliminates a whole category of null-pointer errors. Refactoring this tutorial to use ViewBinding is an excellent intermediate exercise.

5. **Preserve scroll position between tabs.** Replace `replace` with `add` and `hide`, and manage a map of loaded Fragments. This is significantly more complex, but it teaches the trade-offs in Fragment management.

6. **Add a top app bar.** Introduce a `Toolbar` above the `FrameLayout` and update its title based on which tab is selected. This requires passing information from the Activity to the Toolbar, and is a good bridge to the next topic: passing data between components.

---

## Folder Structure After Completion

Your final project should resemble this:

```
app/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/yourname/bottomnavapp/
│       │       ├── MainActivity.kt
│       │       ├── HomeFragment.kt
│       │       ├── SearchFragment.kt
│       │       └── ProfileFragment.kt
│       ├── res/
│       │   ├── drawable/
│       │   │   ├── ic_home.xml
│       │   │   ├── ic_search.xml
│       │   │   ├── ic_profile.xml
│       │   │   └── circle_background.xml     (optional)
│       │   ├── layout/
│       │   │   ├── activity_main.xml
│       │   │   ├── fragment_home.xml
│       │   │   ├── fragment_search.xml
│       │   │   └── fragment_profile.xml
│       │   ├── menu/
│       │   │   └── bottom_nav_menu.xml
│       │   └── values/
│       │       ├── colors.xml
│       │       ├── strings.xml
│       │       └── themes.xml
│       └── AndroidManifest.xml
```

---

## Closing Note

This tutorial has introduced you to one of the most important architectural patterns in modern Android development: the single-Activity, multi-Fragment design. Every major commercial app you have used — Instagram, YouTube, WhatsApp, Spotify — uses some variant of this pattern. The navigation bar at the bottom of those apps is not a separate Activity. It is a single `BottomNavigationView` inside a single Activity, swapping Fragments in and out.

Mastering this pattern is a threshold. Once you understand it, you will find that many problems which once seemed difficult — keeping state across tabs, sharing data between screens, handling back navigation gracefully — become tractable. The Fragment lifecycle, which initially appears intimidating, is really just a set of well-defined moments in a screen's life. Learn to work with it, and Android development becomes dramatically simpler.
