# 快速入门：使用 Media3 构建视频播放器

本指南将向您展示如何使用 Jetpack Media3 库构建一个简单的视频播放器 Android 应用。

## 设置 Android 项目

1.  **添加 Media3 依赖项：**

    将以下依赖项添加到模块的 `build.gradle` 文件中（通常是 `app/build.gradle`）：

    *   **Kotlin DSL (`build.gradle.kts`):**
        ```kotlin
        dependencies {
            implementation("androidx.media3:media3-exoplayer:1.3.1")
            implementation("androidx.media3:media3-ui:1.3.1")
        }
        ```

    *   **Groovy DSL (`build.gradle`):**
        ```groovy
        dependencies {
            implementation 'androidx.media3:media3-exoplayer:1.3.1'
            implementation 'androidx.media3:media3-ui:1.3.1'
        }
        ```
    **注意：** 如果有更新的版本，请将 `1.3.1` 替换为 Media3 的最新版本。您可以在 [Media3 版本说明页面](https://developer.android.com/jetpack/androidx/releases/media3) 找到最新版本。

2.  **启用 Java 8 支持：**

    如果您尚未启用 Java 8 支持，请在同一个 `build.gradle` 文件中启用它。这是 Media3 所必需的。

    *   **Kotlin DSL (`build.gradle.kts`):**
        ```kotlin
        android {
            compileOptions {
                sourceCompatibility = JavaVersion.VERSION_1_8
                targetCompatibility = JavaVersion.VERSION_1_8
            }
            kotlinOptions {
                jvmTarget = "1.8"
            }
        }
        ```

    *   **Groovy DSL (`build.gradle`):**
        ```groovy
        android {
            compileOptions {
                sourceCompatibility JavaVersion.VERSION_1_8
                targetCompatibility JavaVersion.VERSION_1_8
            }
            // 对于 Kotlin 项目
            kotlinOptions {
                jvmTarget = '1.8'
            }
        }
        ```

3.  **添加网络权限：**

    如果您的应用需要从互联网流式传输媒体，请打开应用的 `AndroidManifest.xml` 文件（通常是 `app/src/main/AndroidManifest.xml`）并添加 `INTERNET` 权限：

    ```xml
    <manifest ...>
        <uses-permission android:name="android.permission.INTERNET" />
        <application ...>
            ...
        </application>
    </manifest>
    ```

## 将 PlayerView 添加到布局中

要显示视频和播放控件，请将 `androidx.media3.ui.PlayerView` 添加到您的 XML 布局文件中。例如，在 `activity_main.xml` 中：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.media3.ui.PlayerView
        android:id="@+id/player_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```
此代码段显示了一个填满整个屏幕的 `PlayerView`。您可以调整布局属性以适应应用的设计。

## 在 Activity/Fragment 中初始化播放器

设置好布局后，下一步是初始化 `ExoPlayer` 实例并将其链接到您的 `PlayerView`。这通常在 Activity 的 `onCreate` 方法或 Fragment 的 `onViewCreated` 方法中完成。

操作方法如下：

**1. 声明播放器变量：**
首先，在您的 Activity 或 Fragment 中为 `ExoPlayer` 声明一个变量。

*   **Kotlin (`MainActivity.kt`):**
    ```kotlin
    import androidx.media3.exoplayer.ExoPlayer // 导入 ExoPlayer
    // ... 其他导入

    class MainActivity : AppCompatActivity() {
        private var player: ExoPlayer? = null
        // ...
    }
    ```

*   **Java (`MainActivity.java`):**
    ```java
    import androidx.media3.exoplayer.ExoPlayer; // 导入 ExoPlayer
    // ... 其他导入

    public class MainActivity extends AppCompatActivity {
        private ExoPlayer player;
        // ...
    }
    ```

**2. 在 `onCreate` (Activity) 或 `onViewCreated` (Fragment) 中初始化播放器：**

*   **Kotlin (`MainActivity.kt`):**
    ```kotlin
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import androidx.media3.common.MediaItem
    import androidx.media3.exoplayer.ExoPlayer
    import androidx.media3.ui.PlayerView // 确保导入 PlayerView

    class MainActivity : AppCompatActivity() {

        private var player: ExoPlayer? = null
        private lateinit var playerView: PlayerView // 声明 PlayerView

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main) // 假设您的布局文件是 activity_main.xml

            // 1. 从布局中找到 PlayerView
            playerView = findViewById(R.id.player_view) // 使用您在 XML 中定义的 ID

            // 2. 构建 ExoPlayer 实例
            player = ExoPlayer.Builder(this).build()

            // 3. 将播放器设置到 PlayerView
            playerView.player = player

            // 4. 创建 MediaItem
            val mediaItem = MediaItem.fromUri("https://storage.googleapis.com/exoplayer-test-media-0/BigBuckBunny_320x180.mp4")

            // 5. 将 MediaItem 设置到播放器
            player?.setMediaItem(mediaItem)

            // 6. 准备播放器
            player?.prepare()

            // 7. （可选）立即开始播放
            // player?.playWhenReady = true
        }

        // 记住在 onStop 或 onDestroy 中释放播放器
        override fun onStop() {
            super.onStop()
            player?.release()
            player = null
        }
    }
    ```

*   **Java (`MainActivity.java`):**
    ```java
    import androidx.appcompat.app.AppCompatActivity;
    import android.os.Bundle;
    import androidx.media3.common.MediaItem;
    import androidx.media3.exoplayer.ExoPlayer;
    import androidx.media3.ui.PlayerView; // 确保导入 PlayerView

    public class MainActivity extends AppCompatActivity {

        private ExoPlayer player;
        private PlayerView playerView; // 声明 PlayerView

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main); // 假设您的布局文件是 activity_main.xml

            // 1. 从布局中找到 PlayerView
            playerView = findViewById(R.id.player_view); // 使用您在 XML 中定义的 ID

            // 2. 构建 ExoPlayer 实例
            player = new ExoPlayer.Builder(this).build();

            // 3. 将播放器设置到 PlayerView
            playerView.setPlayer(player);

            // 4. 创建 MediaItem
            MediaItem mediaItem = MediaItem.fromUri("https://storage.googleapis.com/exoplayer-test-media-0/BigBuckBunny_320x180.mp4");

            // 5. 将 MediaItem 设置到播放器
            player.setMediaItem(mediaItem);

            // 6. 准备播放器
            player.prepare();

            // 7. （可选）立即开始播放
            // player.setPlayWhenReady(true);
        }

        // 记住在 onStop 或 onDestroy 中释放播放器
        @Override
        protected void onStop() {
            super.onStop();
            if (player != null) {
                player.release();
                player = null;
            }
        }
    }
    ```
**说明：**
*   我们从布局中获取对 `PlayerView` 的引用。
*   我们使用 `ExoPlayer.Builder` 创建一个 `ExoPlayer` 实例。
*   我们将 `PlayerView` 绑定到 `player` 实例。这允许 `PlayerView` 显示关联播放器的视频和播放控件。
*   我们创建一个指向示例 MP4 视频 URL 的 `MediaItem`。
*   我们告知播放器使用 `setMediaItem()` 加载此 `MediaItem`，然后调用 `prepare()` 开始缓冲媒体。
*   如果希望视频在准备好后立即开始播放，可以取消注释 `player.setPlayWhenReady(true)`（或 Kotlin 中的 `player?.playWhenReady = true`）这一行。
*   在不再需要播放器时（例如，在 `onStop()` 或 `onDestroy()` 中）释放播放器至关重要，以释放资源。

## 实现生命周期管理

正确的生命周期管理对于视频播放器应用至关重要，它可以正确使用编解码器和音频焦点等资源，并确保流畅的用户体验。未能释放播放器可能导致资源泄漏并阻止其他应用使用这些资源。

前面的“初始化播放器”部分展示了在 `onStop()` 中释放播放器的基本方法。本节对此进行了扩展，概述了更强大的生命周期管理，尤其考虑了用于暂停和释放播放器的不同 Android API 级别。

**关键原则：**

*   **延迟初始化，尽早释放：** 在播放器即将变得可见时（例如 `onStart` 或 `onResume`）进行初始化，并在不再需要时（例如 `onPause`、`onStop`、`onDestroy`）立即释放它。
*   **释放资源：** 释放播放器时，调用 `player.release()` 以释放所有底层资源，然后将您的播放器变量设置为 `null`。

**推荐的生命周期集成：**

*   **播放器初始化 (`initializePlayer` 方法)：** 将播放器设置（查找 `PlayerView`、构建 `ExoPlayer`、设置 `MediaItem`、准备播放器）封装在一个辅助方法中。此方法将从 `onStart()` 或 `onResume()` 调用。
*   **播放器释放 (`releasePlayer` 方法)：** 将播放器拆卸（`player.release()`、`player = null`）封装在一个辅助方法中。
*   **`onStart()` (API > 23) 或 `onResume()` (API <= 23):** 在此处初始化播放器。这可确保 Activity 可见时播放器已准备就绪。对于多窗口环境 (API > 23)，首选 `onStart`。
*   **`onPause()` (API <= 23):** 在此处释放播放器。在较旧的 Android 版本中，`onPause` 是 Activity 可能被终止之前的最后一个有保证的调用点。
*   **`onStop()` (API > 23):** 在此处释放播放器。在较新的 Android 版本中，`onStop` 更合适，因为 `onPause` 可能会更频繁地被调用（例如，在多窗口使用期间）。
*   **`onDestroy()`:** 虽然 `onStop` 或 `onPause` 应该处理大多数情况，但最好也在 `onDestroy` 中释放，作为最后的清理，特别是如果您有其他与播放器相关的资源。

以下是如何在您的 Activity 中实现这一点：

*   **Kotlin (`MainActivity.kt`):**
    ```kotlin
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import android.os.Build // 导入 Build
    import androidx.media3.common.MediaItem
    import androidx.media3.exoplayer.ExoPlayer
    import androidx.media3.ui.PlayerView

    class MainActivity : AppCompatActivity() {

        private var player: ExoPlayer? = null
        private lateinit var playerView: PlayerView
        private var playWhenReady = true // 跟踪 playWhenReady 状态
        private var currentItem = 0 // 跟踪当前项索引
        private var playbackPosition = 0L // 跟踪播放位置

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
            playerView = findViewById(R.id.player_view)
        }

        private fun initializePlayer() {
            player = ExoPlayer.Builder(this)
                .build()
                .also { exoPlayer ->
                    playerView.player = exoPlayer
                    // 设置 MediaItem（替换为您的实际媒体）
                    val mediaItem = MediaItem.fromUri("https://storage.googleapis.com/exoplayer-test-media-0/BigBuckBunny_320x180.mp4")
                    exoPlayer.setMediaItem(mediaItem)
                    exoPlayer.playWhenReady = playWhenReady
                    exoPlayer.seekTo(currentItem, playbackPosition)
                    exoPlayer.prepare()
                }
        }

        private fun releasePlayer() {
            player?.let { exoPlayer ->
                playbackPosition = exoPlayer.currentPosition
                currentItem = exoPlayer.currentMediaItemIndex
                playWhenReady = exoPlayer.playWhenReady
                exoPlayer.release()
            }
            player = null
        }

        override fun onStart() {
            super.onStart()
            if (Build.VERSION.SDK_INT > 23) {
                initializePlayer()
            }
        }

        override fun onResume() {
            super.onResume()
            if (Build.VERSION.SDK_INT <= 23 || player == null) {
                initializePlayer()
            }
        }

        override fun onPause() {
            super.onPause()
            if (Build.VERSION.SDK_INT <= 23) {
                releasePlayer()
            }
        }

        override fun onStop() {
            super.onStop()
            if (Build.VERSION.SDK_INT > 23) {
                releasePlayer()
            }
        }
    }
    ```

*   **Java (`MainActivity.java`):**
    ```java
    import androidx.appcompat.app.AppCompatActivity;
    import android.os.Bundle;
    import android.os.Build; // 导入 Build
    import androidx.media3.common.MediaItem;
    import androidx.media3.exoplayer.ExoPlayer;
    import androidx.media3.ui.PlayerView;

    public class MainActivity extends AppCompatActivity {

        private ExoPlayer player;
        private PlayerView playerView;
        private boolean playWhenReady = true; // 跟踪 playWhenReady 状态
        private int currentItem = 0; // 跟踪当前项索引
        private long playbackPosition = 0L; // 跟踪播放位置

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            playerView = findViewById(R.id.player_view);
        }

        private void initializePlayer() {
            player = new ExoPlayer.Builder(this).build();
            playerView.setPlayer(player);

            // 设置 MediaItem（替换为您的实际媒体）
            MediaItem mediaItem = MediaItem.fromUri("https://storage.googleapis.com/exoplayer-test-media-0/BigBuckBunny_320x180.mp4");
            player.setMediaItem(mediaItem);
            player.setPlayWhenReady(playWhenReady);
            player.seekTo(currentItem, playbackPosition);
            player.prepare();
        }

        private void releasePlayer() {
            if (player != null) {
                playbackPosition = player.getCurrentPosition();
                currentItem = player.getCurrentMediaItemIndex();
                playWhenReady = player.getPlayWhenReady();
                player.release();
                player = null;
            }
        }

        @Override
        protected void onStart() {
            super.onStart();
            if (Build.VERSION.SDK_INT > 23) {
                initializePlayer();
            }
        }

        @Override
        protected void onResume() {
            super.onResume();
            if (Build.VERSION.SDK_INT <= 23 || player == null) {
                initializePlayer();
            }
        }

        @Override
        protected void onPause() {
            super.onPause();
            if (Build.VERSION.SDK_INT <= 23) {
                releasePlayer();
            }
        }

        @Override
        protected void onStop() {
            super.onStop();
            if (Build.VERSION.SDK_INT > 23) {
                releasePlayer();
            }
        }
    }
    ```
`initializePlayer` 方法现在还包含保存和恢复播放器状态（`playWhenReady`、`currentItem`、`playbackPosition`）的逻辑，以便在应用返回前台时无缝恢复播放。

## (可选) 添加播放控件

`androidx.media3.ui.PlayerView` 组件会自动提供一组默认的 UI 控件用于播放，包括播放/暂停按钮、搜寻栏和时间显示。当您将 `ExoPlayer` 实例设置到 `PlayerView` (`playerView.player = player`) 时，这些控件会自动链接到您的播放器。

对于大多数用例，`PlayerView` 提供的默认控件已足够，您无需实现自定义控件。

但是，您也可以通过 `ExoPlayer` 实例以编程方式直接控制播放。如果您想实现自定义 UI 元素或根据应用中的其他事件触发播放操作，这将非常有用。

以下是一些常见的编程播放控件：

*   **播放/暂停：**
    *   `player.play()`: 开始播放。这是 `player.setPlayWhenReady(true)` 的便捷方法。
    *   `player.pause()`: 暂停播放。这是 `player.setPlayWhenReady(false)` 的便捷方法。
    *   `player.setPlayWhenReady(shouldPlay: Boolean)`: 设置播放器的播放意图。如果为 `true`，一旦播放器准备就绪（例如，媒体已缓冲），播放就会开始。如果为 `false`，播放将暂停。

*   **搜寻：**
    *   `player.seekTo(positionMs: Long)`: 搜寻到当前媒体项中的特定位置，以毫秒为单位指定。

**代码示例：**

假设您已如前几节所示初始化了 `player`（ExoPlayer 实例）。

*   **Kotlin (`MainActivity.kt`):**
    ```kotlin
    // 开始或恢复播放
    player?.play()

    // 暂停播放
    player?.pause()

    // 搜寻到视频的 10 秒处
    player?.seekTo(10_000L) // 位置（毫秒）

    // 控制播放意图（例如，基于自定义按钮）
    val shouldPlayVideo = true // 或 false
    player?.playWhenReady = shouldPlayVideo
    ```

*   **Java (`MainActivity.java`):**
    ```java
    // 开始或恢复播放
    if (player != null) {
        player.play();
    }

    // 暂停播放
    if (player != null) {
        player.pause();
    }

    // 搜寻到视频的 10 秒处
    if (player != null) {
        player.seekTo(10_000L); // 位置（毫秒）
    }

    // 控制播放意图（例如，基于自定义按钮）
    boolean shouldPlayVideo = true; // 或 false
    if (player != null) {
        player.setPlayWhenReady(shouldPlayVideo);
    }
    ```

请记住，`playWhenReady` 表示所需的播放状态。只有当 `playWhenReady` 为 `true` 且播放器状态为 `Player.STATE_READY` 时，播放器才会实际播放。`play()` 和 `pause()` 方法是管理 `playWhenReady` 标志的便捷方式。

## 最小示例

以下是一个完整的 Android Activity 示例，演示了如何播放一个示例视频 URL。您可以将其用作构建自己的视频播放器应用的起点。

**1. 布局文件 (`activity_main.xml`):**
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.media3.ui.PlayerView
        android:id="@+id/player_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

**2. Kotlin Activity (`MainActivity.kt`):**
```kotlin
package com.example.myvideoplayer // 替换为您的包名

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.os.Build
import androidx.media3.common.MediaItem
import androidx.media3.exoplayer.ExoPlayer
import androidx.media3.ui.PlayerView
import com.example.myvideoplayer.databinding.ActivityMainBinding // 假设您使用视图绑定

class MainActivity : AppCompatActivity() {

    private var player: ExoPlayer? = null
    private lateinit var binding: ActivityMainBinding // 使用视图绑定

    private var playWhenReady = true
    private var currentItem = 0
    private var playbackPosition = 0L
    private val videoUrl = "https://storage.googleapis.com/exoplayer-test-media-0/BigBuckBunny_320x180.mp4"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
    }

    private fun initializePlayer() {
        player = ExoPlayer.Builder(this)
            .build()
            .also { exoPlayer ->
                binding.playerView.player = exoPlayer // 将 PlayerView 从绑定中分配
                val mediaItem = MediaItem.fromUri(videoUrl)
                exoPlayer.setMediaItem(mediaItem)
                exoPlayer.playWhenReady = playWhenReady
                exoPlayer.seekTo(currentItem, playbackPosition)
                exoPlayer.prepare()
            }
    }

    private fun releasePlayer() {
        player?.let { exoPlayer ->
            playbackPosition = exoPlayer.currentPosition
            currentItem = exoPlayer.currentMediaItemIndex
            playWhenReady = exoPlayer.playWhenReady
            exoPlayer.release()
        }
        player = null
    }

    override fun onStart() {
        super.onStart()
        if (Build.VERSION.SDK_INT > 23) {
            initializePlayer()
        }
    }

    override fun onResume() {
        super.onResume()
        if (Build.VERSION.SDK_INT <= 23 || player == null) {
            initializePlayer()
        }
    }

    override fun onPause() {
        super.onPause()
        if (Build.VERSION.SDK_INT <= 23) {
            releasePlayer()
        }
    }

    override fun onStop() {
        super.onStop()
        if (Build.VERSION.SDK_INT > 23) {
            releasePlayer()
        }
    }
}
```

**3. Java Activity (`MainActivity.java`):**
```java
package com.example.myvideoplayer; // 替换为您的包名

import androidx.appcompat.app.AppCompatActivity;
import android.os.Build;
import android.os.Bundle;
import androidx.media3.common.MediaItem;
import androidx.media3.exoplayer.ExoPlayer;
import androidx.media3.ui.PlayerView;
// 如果不使用视图绑定，请确保 PlayerView 的 ID 在 R.java 文件中可用
// import com.example.myvideoplayer.R; // 如果需要

public class MainActivity extends AppCompatActivity {

    private ExoPlayer player;
    private PlayerView playerView; // 直接引用 PlayerView

    private boolean playWhenReady = true;
    private int currentItem = 0;
    private long playbackPosition = 0L;
    private String videoUrl = "https://storage.googleapis.com/exoplayer-test-media-0/BigBuckBunny_320x180.mp4";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main); // 设置布局
        playerView = findViewById(R.id.player_view); // 从布局中查找 PlayerView
    }

    private void initializePlayer() {
        player = new ExoPlayer.Builder(this).build();
        playerView.setPlayer(player);

        MediaItem mediaItem = MediaItem.fromUri(videoUrl);
        player.setMediaItem(mediaItem);
        player.setPlayWhenReady(playWhenReady);
        player.seekTo(currentItem, playbackPosition);
        player.prepare();
    }

    private void releasePlayer() {
        if (player != null) {
            playbackPosition = player.getCurrentPosition();
            currentItem = player.getCurrentMediaItemIndex();
            playWhenReady = player.getPlayWhenReady();
            player.release();
            player = null;
        }
    }

    @Override
    protected void onStart() {
        super.onStart();
        if (Build.VERSION.SDK_INT > 23) {
            initializePlayer();
        }
    }

    @Override
    protected void onResume() {
        super.onResume();
        if (Build.VERSION.SDK_INT <= 23 || player == null) {
            initializePlayer();
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
        if (Build.VERSION.SDK_INT <= 23) {
            releasePlayer();
        }
    }

    @Override
    protected void onStop() {
        super.onStop();
        if (Build.VERSION.SDK_INT > 23) {
            releasePlayer();
        }
    }
}
```

**确保您已按照“设置 Android 项目”部分中的说明配置了您的 `build.gradle` 文件和 `AndroidManifest.xml` 文件。**

## 结论

本指南介绍了使用 Media3 库在 Android 应用中实现基本视频播放器的核心步骤。通过遵循这些说明，您可以快速设置和自定义您的视频播放体验。
