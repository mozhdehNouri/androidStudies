

# Android Development Best Practices

when any single application asks for memory, if there isn’t any free memory, Android will respond by closing other apps so it can provide that memory



-  Use the recommended Android architecture 

- Start reading other's code to learn the best practices

- Use the Android Architecture Components
  
  - This includes the ViewModel, LiveData, and Room components, which can help you write more maintainable and reliable code

- Detect and Fix memory leaks in Android App

-  Use Proguard in your release version

-  Use debugging tools
  
  - [Android Debug Database](https://github.com/amitshekhariitbhu/Android-Debug-Database).   for debugging database and sharedPrefrence
  
  - Adding text as string resources are always useful in the long-run, especially when support for new languages needs to be added.

- Use strings.xml

-  Create separate layouts for UI elements that will be re-used
  
  - Use the `include` tag in xml. Another handy tag is the `merge` tag. It acts as a pseudo parent and helps get rid of an unneeded root ViewGroup.

-  Use shapes and selectors instead of images as much as possible
  
  - Using shapes and selectors will further reduce APK size.

-  Avoid deep levels in layouts
  
  - A deep hierarchy of views makes your UI slow, not to mention making it harder to manage your layouts. Deep hierarchies can mostly be avoided by using the correct ViewGroup. Use Constraint Layout.

-  Use the Parcelable class instead of Serializable when passing data in Intents or Bundles



-   Perform file operations off the UI thread

- Understand Bitmaps
  
  - As it takes a huge amount of memory, it can lead to OOM easily. Users love content! Especially when the content is well-formatted and looks nice. Images, for instance, are extremely nice content, mainly due to their property of conveying a thousand words per image. They also consume a lot of memory. A lot of memory!.
  
  - [Bitmap pool](https://amitshekhar.me/blog/bitmap-pool)
  
  - [How does the Android Image Loading library work internally?](https://amitshekhar.me/blog/android-image-loading-library-use-bitmap-pool-for-responsive-ui)



- Understand the Context in Android
  
  Learning what is the Context in Android and using it correctly is important to avoid memory leaks in Android. Learn from [here](https://amitshekhar.me/blog/context-in-android-application).

-  Use styles to avoid duplicate attributes in layout XMLs

- Always include unit tests

- Optimize your build speed
  
  - Long build times slow down your development process. Start optimizing for the build speed.

-  Secure your Android Application
  
  -  decompiled, reverse-engineered

-  Use proven libraries instead of your own solutions

-  Design for Multiple Screen Sizes and Resolutions

-  Use the Latest SDKs and Libraries
  
  - Keep your development environment up-to-date by using the latest Android SDKs, libraries, and tools

-  Test on various OS versions
  
  - And again, finally, keep testing on various OS versions.



Resource :

[Android Development Best Practices](https://amitshekhar.me/blog/android-development-best-practices)
