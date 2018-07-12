## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/yannanzheng/yannanzheng.github.io/edit/master/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/yannanzheng/yannanzheng.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.


#Android-Studio离线升级——老项目搬迁
>几乎是离线模式下进行开发。项目的开发环境较老，使用的还是Android Studio1.2.2版本。目前最新版本已经达到了额3.1.2。我实在难以忍受这么老的开发环境，因此折腾升级AndroidStudio。
##1 将Android Studio从1.2.2升级到2.3.3
>之前尝试将项目一步到位从1.2.2一步到位升级到3.1.2，问题实在太多，基本失败。因此尝试先升级到2.3.3，以后再想办法升级到最新版本。
###1.1搭建Android Studio2.3.3环境
>由于公司电脑主要问题在于无法连接搭建环境需要的外网，可以从外部copy文件到公司电脑，但不能拷出。因此最便捷的方式是将外部可用相应系统的开发环境通过copy的方式迁移到公司电脑上。我自己电脑上有一个2.3.3，因此准备将2.3.3迁移到公司电脑。

将可用的AndroidStudio2.3.3从外部电脑拷贝进公司电脑，包括相应的sdk和用户目录下的.gradle文件夹、.AndroidStudio2.3文件夹、.android文件夹等，copy过程中覆盖选项 选全否。
copy完成，打开AndroidStudio2.3.3运行，创建新文件可用。可以算作基本环境搭建完成。
###1.2迁移项目到新环境
####1.2.1使用项目之前使用的的sdk和gradle，即使用AndroidStudio1.2.2上的sdk和gradle
clean
build
run
安装
点击运行，没问题。
####1.2.2升级gradle
1）将相关的build.gradle中的 
```
classpath 'com.android.tools.build:gradle:1.2.3'
```
改成
```
classpath 'com.android.tools.build:gradle:2.3.3'
```
2）将根目录gradle目录里的gradle-wrapper.properties的
```
distributionUrl=https\://services.gradle.org/distributions/gradle-2.2-all.zip
```
改成
```
distributionUrl=https\://services.gradle.org/distributions/gradle-3.3-all.zip
```
3）编译并将项目在真机上测试
编译成功
安装成功
运行过程中crash，报错
```
E/baidumapsdk: Authentication Error errorcode: 230 uid: -1 appid -1 msg: APP Scode码校验失败
```
看来是百度sdk那边出问题了。
搜索了下，看到文章
https://blog.csdn.net/leaf_130/article/details/51377049?locationNum=13&fps=1
初步判断是debug的key的原因。

将项目sdk改成老android studio1.2.2使用的sdk
编译成功
运行安装报错
```
duplicate entry: com/google/android/gms/dynamic/zza.class
```
搜索参考了[文章](https://blog.csdn.net/mvpstevenlin/article/details/57075112)，应该是包引重了。
定位zza.class类，发现属于google-play-service，搜索定位到应用的项目依赖里面的jar包重复，删除了该jar包。运行成功。

再重新将项目sdk改成copy进来的Android Studio2.3.3使用的sdk
clean
rebuild
run安装到真机
测试
不再报错

##2 将Android Studio从2.3.3升级到Android Studio3.0.1
>上面已经完成了从Android Studio1.2.2到2.3.3的升级，老项目也成功运行没问题。继续折腾Android Studio及gradle的升级。

Android Studio3.0.1的默认gradle插件版本自然就是3.0.1了
```
classpath 'com.android.tools.build:gradle:3.0.1'
```
而相应的默认gradle版本是4.1
```
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
```
该版本已经开始使用**implementation**代替**compile**但是还支持使用**compile**。老项目迁移过来应该也能直接运行，很适合作为逐步升级的中间环节。即使后面的升级不成功，该版本用起来也还不错吧！
###2.1 搭建Android Stuido3.0.1环境
创建Android Studio3.0.1文件夹，依然是从外部拷如Android Studio3.0.1
将前面Android Studio2.3.3使用的sdk拷过来一份，主要为了不相互影响；
打开Android Studio3.0.1，将sdk设置为刚拷入得sdk；
创建新项目，报错
```
Error:Failed to find target with hash string 'android-26' in: D:\Android3.01\ASSDK

Consult IDE log for more details (Help | Show Log)
<a href="install.android.platform">Install missing platform(s) and sync project</a>
```
将compileSdkVersion，targetSdkVersion改为25
将
```
implementation 'com.android.support:appcompat-v7:26.0.0-beta1'
```
改为
```
implementation 'com.android.support:appcompat-v7:25.0.0+'
```
编译运行成功，说明开发环境搭建基本可以使用了，只是编译工具还没能升级到26。

###2.2 迁移项目到Android Studio3.0.1
打开时后选择使用Android Studio的sdk，将build.gradle里面的gradle插件改为版本改为3.0.1，gradle版本改为4.1，编译报错
```
Warning:The specified Android SDK Build Tools version (25.0.0) is ignored, as it is below the minimum supported version (26.0.2) for Android Gradle Plugin 3.0.1.
Android SDK Build Tools 26.0.2 will be used.
To suppress this warning, remove "buildToolsVersion '25.0.0'" from your build.gradle file, as each version of the Android Gradle Plugin now has a default version of the build tools.
<a href="fix.build.tools.version">Update Build Tools version and sync project</a><br><a href="openFile:C:/Users/0288000131/Desktop/wing_source/Wing/build.gradle">Open File</a>

Error:Failed to resolve: com.android.support:multidex:1.0.2
<a href="add.google.maven.repository">Add Google Maven repository and sync project</a><br><a href="openFile:C:/Users/0288000131/Desktop/wing_source/Wing/build.gradle">Open File</a><br><a href="open.dependency.in.project.structure">Show in Project Structure dialog</a>

Error:Failed to resolve: com.android.support:multidex-instrumentation:1.0.2
<a href="add.google.maven.repository">Add Google Maven repository and sync project</a><br><a href="openFile:C:/Users/0288000131/Desktop/wing_source/Wing/build.gradle">Open File</a><br><a href="open.dependency.in.project.structure">Show in Project Structure dialog</a>
```

说的很明白了，最低要使用26.0.2版本的sdk构建工具才行。点击默认的修复，依然会报错
```
Error:Failed to resolve: com.android.support:multidex:1.0.2
<a href="openFile:C:/Users/0288000131/Desktop/wing_source/Wing/build.gradle">Open File</a><br><a href="open.dependency.in.project.structure">Show in Project Structure dialog</a>

Error:Failed to resolve: com.android.support:multidex-instrumentation:1.0.2
<a href="openFile:C:/Users/0288000131/Desktop/wing_source/Wing/build.gradle">Open File</a><br><a href="open.dependency.in.project.structure">Show in Project Structure dialog</a>
```
关闭multidex
```
	defaultConfig {
//        multiDexEnabled true
    }

```
编译运行成功，但是报错
```
baidumapsdk: Authentication Error errorcode: -1 uid: -1 appid -1 msg: Current ne
```
看来是debug.key有问题
使用Everything软件搜索debug.key，发现项目下其实有一个debug.keystore文件，.android目录下也有该文件，看来默认使用的是.android目录下的debug.keystore。在build.gradle中指定使用项目目录下的debug.keystore，百度相关的错误消失，出现新错误。
```
E/dex2oat: Failed to create oat file:***: Permission denied

```
搜索了下，在stackoverflow上找到一篇[文章](https://stackoverflow.com/questions/46069444/e-dex2oat-failed-to-create-oat-file)，于是照着关闭了instant run，编译运行成功，不报错。不过目前使用的sdk版本是25.0.0，gradle插件用的2.3.3，gradle版本用的是3.3。将gradle插件换成3.0.1及gradle-4.1无法编译通过，一直报告缺少multidex1.0.2。由于无法联网更新，暂时无法更新到3.0.1的插件了。
升级编译的sdk
```
compileSdkVersion 26
buildToolsVersion '26.0.2'
```
编译运行没问题

####验证使用错了debug.keystore
把环境切换回Android Studio2.3.3的默认环境，显式指定debug.keystore目录为项目目录下的debug.keystore，编译运行成功，而且之前百度地图只出现网格的情况也消失。说明自己之前其实一直使用了错误的keystore。

##3 总结
**Android Studio**从1.2.2升级到2.3.3成功；
**Android Studio**从2.3.3升级到3.0.1成功；
**gradle插件**从1.2.3升级到2.3.3成功；
**gradle插件**从2.3.3升级到3.0.1失败；
把compileSdkVersion从25更新到26成功








