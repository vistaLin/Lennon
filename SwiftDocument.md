#Swift study

[toc]

##oc与swift的混编

###oc调用swift

- 需要在oc中导入:projectName-swift.
> 该文件无需自己生成,会自动导入所有的swift的类。
- 如果需要暴露函数给oc，需要在函数前加入@objc,如下:

```oc

@objec func test(){

}
```

- 如果需要将属性暴露给oc,需要在加入@objcMembers,如下:

```OC
@objecMembers
class test: NSObject {

}
```

- **如果找不到方法,可能是projectName-Swift中没有生成只有删除重新生成**

###swift调用oc

- 当在oc项目中新建swift类的时候会自动提示创建桥接文件;如果选择放弃,可以创建一个header文件,命名格式:objectName-bridging-Header.h,并且在build setting->swift compiler-Geneal中直接拖入.h文件,并且在项目名的地方截断.
- 将需要在swift中调用的类,直接导入这个桥接文件即可.

###"projectName-Swift"文件找不到

- 可以查看TARGETS-> Build Setting - Switft Compiler - General -> Object-C Genearted interface header name 名称是否一致?
 - 如果不一致可以通过修改build setting里面的packaging 的Product Name来和项目名一致
> 请不要随意修改xxx-swift名称,否则前面添加的swift类在xxx-swift会找不到

- 直接前往"~/Library/Developer/Xcode/DerivedData/",然后点击项目名->Build->Intermediates.noindex->projectName.build->Debug-iphoneos->projectName.build->DerivedSources,看看projectName-swift是否存在?可能是因为称为了APP中文名-swift,可以修改称为项目的英文名,编译过后重新修改称为APP的中文名称。如果不是这个原因，在看是否是循环引用等。

[一个关于xCode11（报找不到 PRODUCT_NAME-Swift.h 文件）的一个问题](https://www.jianshu.com/p/1a2851c11386)

##知识点

###oc的类方法和扩展与swift的调用

- swift无法调用oc的类方法,swift可以用class或者static修饰成为类方法.
- swift直接extension来新建类扩展,如:

```OC
extension UIColor {

}
```

###定义swift枚举值

- 使用enum来定义,可以定义不同类型的值,且要无默认值,例如:

```OC
enum PPNetworkStatusType : Int {
    case PPNetworkStatusUnknown = 0 /** 未知网络*/
    case PPNetworkStatusNotReachable = 1 /** 无网络*/
    case PPNetworkStatusReachableViaWWAN = 2 /** 手机网络*/
    case PPNetworkStatusReachableViaWiFi = 3 /** WIFI网络*/
}
```

###访问控制关键private,fileprivate,interal,public,open

- private:只能在当前类访问。
 - 类的extension也可以访问。
- fileprivate:在当前Swift源文件可以访问.
- internal:在源代码的整个模块可以访问.
- public(默认修饰属性): 可以被任何人访问,但其它module不可以被override和继承,在module内可以.
- open:可以被任何人使用,且override和集成.
- 权限排序:open>public>interal>fileprivate>private

###?和!

- ?声明为可选类型,无初始值赋值为nil,无可选类型值为空;对变量操作前添加?,判断为nil,则不执行后面的方法;对变量操作添加!表示强制解包,解析肯定有值,nil会导致崩溃.
- !声明为可选类型,并且后序操作都隐式添加了!,表示默认为非nil,直接进行解包.

###final

- final表示不允许对其修饰的内容进行继承或者重新操作

##设计模式

###单例模式

- 单例的优点
 - 提供了对唯一实例的受控访问,防止其它对象对自己的实例化
 - 节约系统资源,只存在一个对象,对于需要频繁创建和销毁的对象,单例可以提升系统的性能
 - 避免对资源的多重占用,只有一个实例存在内存中,避免对同一资源文件的同时写操作.

####swift实现单例模式

#####方式一:将实例定义为全局变量.

- swift的全局变量初始默认使用dispatch_once,保证了全局变量的构造器只会被调用一次,全局变量也是懒加载

```OC
let shareManager = MyManager()
class MyManager {
  init() {
  
  }
}
```

######方式2:使用static

- static限定变量的作用,也保证不被继承,加入private关键字,保证单例的原子性

```OC
class MyManager {
  static let shared = MyManger()
  private init() {
  
  }
}
```