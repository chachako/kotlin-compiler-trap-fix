# kotlin-compiler-trap-fix
记录开发 Kotlin 编译器插件时的踩坑日常

## IR Backend

> `No mapping for symbol: VALUE_PARAMETER INSTANCE_RECEIVER name:<this> type:<root>.MyClass`

**解决方式**
```diff
- val dispatchReceiver = parentAsClass.thisReceiver!!.copyTo(constructor)
+ val dispatchReceiver = parentAsClass.thisReceiver!!
```
原因是因为 JVM 字节码中的 `this` 在构造函数中是直接访问的，而在常规函数中将会为 this 创建一个临时变量，因此 `IrConstructor.body` 中不需要使用 [IrValueParameter.copyTo](https://github.com/JetBrains/kotlin/blob/1.6.20/compiler/ir/backend.common/src/org/jetbrains/kotlin/backend/common/ir/IrUtils.kt#L113) 来复制 `class.thisReceiver`
