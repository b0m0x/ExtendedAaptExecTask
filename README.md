Extended AaptExecTask, for use with Android SDK r21
=====================

Extended AaptExecTask is a replacement for the vanilla AaptExecTask that allows you to define the package of the automatically generated R class.
This is especially usefull if you want to build multiple versions of your app (e.g. free and paid) without changing every R import of every source file.
Since the original android sdk does include this option in its ant build.xml, I extended the AaptExecTask which is responsible for passing arguments to aapt.

(Note that this is specific to the current SDK implementation and will likely break in future revisions of the Android SDK. If it's possible, consider using a library project as a common base to your different app versions. If this is no option for you, this is the way to go until Google implements this themselves in the SDK. I will try to keep this project updated as long as possible, but no promises here!)


Dependencies
-----

Add the following .jars to your build path:

* anttasks.jar
* sdklib.jar
* common.jar
* manifmerger.jar
* ant.jar 

All jars except ant.jar come with the android sdk and are located in the "tools/lib" folder.

Install
-----
a) Open the project in eclipse and export it as jar. Save it under $ANDROID_SDK/tools/lib/customanttasks.jar

b) If you haven't done it already, I recommend to copy the build.xml from $ANDROID_SDK/tools/ant/ to your project directory, but it's optional.
Open your build.xml, search the following lines:

```xml
<path id="android.antlibs">
    <pathelement path="${sdk.dir}/tools/lib/anttasks.jar" />
</path>

<!-- Custom tasks -->
<taskdef resource="anttasks.properties" classpathref="android.antlibs" />
```

Change them to 
```xml
<path id="android.antlibs">
    <pathelement path="${sdk.dir}/tools/lib/anttasks.jar" />
    <pathelement path="${sdk.dir}/tools/lib/customanttasks.jar" />
</path>

<!-- Custom tasks -->
<taskdef resource="anttasks.properties" classpathref="android.antlibs" />
<taskdef resource="customanttasks.properties" classpathref="android.antlibs" />
```




Then search your aapt task, it is located within the code-gen target and looks like
```xml
<aapt executable="${aapt}"
      command="package"
      verbose="${verbose}"
      manifest="${out.manifest.abs.file}"
      androidjar="${project.target.android.jar}"
      rfolder="${gen.absolute.dir}"
      nonConstantId="${android.library}"
      libraryResFolderPathRefid="project.library.res.folder.path"
      libraryPackagesRefid="project.library.packages"
      ignoreAssets="${aapt.ignore.assets}"
      libraryRFileRefid="project.library.bin.r.file.path"
      binFolder="${out.absolute.dir}"
      proguardFile="${out.absolute.dir}/proguard.txt">
        <res path="${out.res.absolute.dir}" />
        <res path="${resource.absolute.dir}" />
</aapt>
```
            
Replace it with the following lines:
```xml
<property name="gen.r.package" value="${project.app.package}" />

<eaapt executable="${aapt}"
       command="package"
       verbose="${verbose}"
       manifest="${out.manifest.abs.file}"
       androidjar="${project.target.android.jar}"
       rfolder="${gen.absolute.dir}"
       nonConstantId="${android.library}"
       libraryResFolderPathRefid="project.library.res.folder.path"
       libraryPackagesRefid="project.library.packages"
       ignoreAssets="${aapt.ignore.assets}"
       libraryRFileRefid="project.library.bin.r.file.path"
       binFolder="${out.absolute.dir}"
       proguardFile="${out.absolute.dir}/proguard.txt"
       customPackage="${gen.r.package}">
        <res path="${out.res.absolute.dir}" />
        <res path="${resource.absolute.dir}" />
</eaapt>
```


Thats it! 
If you want to keep the default package for now, do nothing else. 
If you want to set a custom package for the R class, simply add the following line into project.properties in your project directory:

```xml
gen.r.package=your.custom.package
```
