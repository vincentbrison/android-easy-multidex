android-easy-multidex
=====================

Sample project illustrating how to overcome the 65536 methods dex limitation.
Based on the awesome [article](http://blog.osom.info/2014/10/multi-dex-to-rescue-from-infamous-65536.html) 
of [@alex_lipov](https://twitter.com/alex_lipov).

Easy setup
==========
To be able to overcome the 65536 methods dex limitation, simply do the following steps :
1. Add to your `android` closure inside your `build.gradle` :
```gradle
dexOptions {
    preDexLibraries = false
}
```
2. Add the following closure to your `build.gradle`:
```gradle
afterEvaluate {
    tasks.matching {
        it.name.startsWith('dex')
    }.each { dx ->
        if (dx.additionalParameters == null) {
            dx.additionalParameters = []
        }
        dx.additionalParameters += '--multi-dex'
        dx.additionalParameters += "--main-dex-list=$projectDir/multidex.keep".toString()
    }
}
```
3. Create the file `multidex.keep` in the same folder than the `build.gradle` with the following content :
```
android/support/multidex/BuildConfig.class
android/support/multidex/MultiDex$V14.class
android/support/multidex/MultiDex$V19.class
android/support/multidex/MultiDex$V4.class
android/support/multidex/MultiDex.class
android/support/multidex/MultiDexApplication.class
android/support/multidex/MultiDexExtractor$1.class
android/support/multidex/MultiDexExtractor.class
android/support/multidex/ZipUtil$CentralDirectory.class
android/support/multidex/ZipUtil.class
```
4. Add inside your `libs` folder the `android-support-multidex.jar` located here :
`ANDROID_HOME/extras/android/support/multidex/library/libs`
5. If you do not subclassing the Application class, add in your `AndroidManifest.xml`, into the `application` element, the attribut `android:name="android.support.multidex.MultiDexApplication"`
6. If you are actually subclassing the Application class, make it extending the `MultiDexApplication` class.

Here you are, you do not have to bother anymore with dex limitation on number of methods. Keep in mind that it
will only work on ICS (API 14) and above.
