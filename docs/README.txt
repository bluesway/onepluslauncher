How to include Shelf SDK into your project
1. create a 'libs' directory in your project root
2. put ShelfLib-release-x.y.z into 'libs'
3. in your build.gradle, add following line in the dependencies section:
    compile fileTree(include: ['*.jar'], dir: 'libs')
4. if your project hasn't compile gson library yet, just compile it as well:
    compile 'com.google.code.gson:gson:2.8.0'