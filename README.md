#Nice apk

让编译之后生成的apk文件可读性更好，可以在文件名种包含version name, version code和友盟渠道。这时候我们把这个apk文件给运营人员，就可以让他们闭嘴了。

#使用方法

只需要在[build.gradle](https://github.com/ufo22940268/niceapk/blob/master/app/build.gradle)中的release标签中加入下面代码：

        def manifestParser = new DefaultManifestParser()
        applicationVariants.all { variant ->
            def vn = manifestParser.getVersionName(android.sourceSets.main.manifest.srcFile)
            def vc = manifestParser.getVersionCode(android.sourceSets.main.manifest.srcFile)

            //Get umeng channel
            def gradleScript = android.sourceSets.main.manifest.srcFile
            def line = 0
            def uv = ""
            gradleScript.eachLine {
                if (line == -1) {
                    uv = it
                    uv = uv.tokenize('"')[1]
                    line = 0
                    return
                }


                if (it.contains("UMENG_CHANNEL")) {
                    line = -1
                } else {
                    line += 1
                }
            }

            def file = variant.outputFile
            variant.outputFile = new File(file.parent, file.name.replace(".apk", "-name-" + vn + "-code-" + vc + "-channel-" + uv + ".apk"))
        }

这时候，运行在终端中运行gradle assembleRelease就能看到生成类似于下面的文件名:

    app-release-unsigned-name-1.0-code-12-channel-Xiaomi.apk
