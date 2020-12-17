#优化App包的大小

##针对D豆树的优化

- 初始大小为708M,.ipa包的大小为40.1MB(已经优化过图片的),线上包大小显示为53.4MB.

###图片资源优化


####1.将启动图片放在了Assets.xcassets


Showing Recent Messages
CpResource /Users/miya/Desktop/yami/DDouShu/D豆树-iOS/Dtree/DdouTree/第三方/刷新动画/LNHeaderTmallAnimator/Tmall/frontpage_refresh_release@2x.gif /Users/miya/Library/Developer/Xcode/DerivedData/DdouTree-anmbdxxrnvkgrlbqtxzscjqvwwat/Build/Products/Debug-iphoneos/DdouTree.app/frontpage_refresh_release@2x.gif (in target 'DdouTree' from project 'DdouTree')
    cd /Users/miya/Desktop/yami/DDouShu/D豆树-iOS/Dtree
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -resolve-src-symlinks /Users/miya/Desktop/yami/DDouShu/D豆树-iOS/Dtree/DdouTree/第三方/刷新动画/LNHeaderTmallAnimator/Tmall/frontpage_refresh_release@2x.gif /Users/miya/Library/Developer/Xcode/DerivedData/DdouTree-anmbdxxrnvkgrlbqtxzscjqvwwat/Build/Products/Debug-iphoneos/DdouTree.app

error: /Users/miya/Desktop/yami/DDouShu/D豆树-iOS/Dtree/DdouTree/第三方/刷新动画/LNHeaderTmallAnimator/Tmall/frontpage_refresh_release@2x.gif: No such file or directory (in target 'DdouTree' from project 'DdouTree')



####2.将未使用的图片删除

- 下载[LSUnusedResources](https://github.com/tinymind/LSUnusedResources),运行拖入项目路径,点击底部搜索,删除未使用到的图片.


####3.将PNG图片转换称为WebP,在CPU资源消耗上和解码时间上比PNG高了两倍.未尝试...




####3.降低图片大小的

##优化方案

###针对图片资源的优化

1.将所有图片资源放入Assets.xcassets,这样打包的时候苹果会自动给我们压缩.
2.使用LSUnusedResources工具清除无效的图片资源,记得手动删除,因为.gif图检测不出来,删除会导致gif失效.
3.将PNG图片转换称为WebP图片,但是在cpu和解码时间上增加了2倍.

###针对于无用代码的优化

1.代码不超过百万行可以使用AppCode来检查无用的代码,但是要经过人工确认再删除,因为如子类使用父类的方法,父类的这个方法不会被任务使用了等.