# Acceleo Language 笔记 

Acceleo是OMG的MOF Model to Text Language (MTL)标准的实现，由法国Obeo公司研发，专用于MDA过程中的代码生成。由于它可以定义自己的目标生成语言，于是也是一个模型转换工作常用的选择。

Acceleo Language 被称作是 Model to Text Language,它是由两个核心的部分组成，分别就是模板【templates】和查询【queries】

## Modules

一个Acceleo的module是以“.mtl”结尾的文件，该文件包含了生成文本的模板【templates】，或者是查询【queries】（从模型中获取信息）。

文件必须以下面的module声明开始：

```
[module <module_name>('metamodel_URI_1', 'metamodel_URI_2')]
```

一个module能够继承extend另一个module，在这种情况下它的模板templates能够重写它父module的“public”和“protected”模板templates。当然它也能够直接获得它的父module的“public”和“protected”模板templates以及查询queries。


**import**

一个module的执行通常依赖于其他的module。因此需要用到import来声明它们，语义如下：

```
import qualified::name::of::imported::module
```

![import1](http://wiki.eclipse.org/images/b/b2/Acceleo-userguide-import.png)

使用快捷键ctrl+Space可以得到能够导入的modules。

![import2](http://wiki.eclipse.org/File:Acceleo-userguide-import-completion.png)


**Static Overriding**
Acceleo允许你通过重写一些行为的一部分templates(除了private的template)来继承其他的module，这有点像面向对象的编程。

[这里是视频链接](https://youtu.be/AGsElJSvTqQ)


## Templates

模板Templates用以生成文本，由```[template...][/template]```标签包围。

![templates](http://wiki.eclipse.org/images/3/35/Acceleo-userguide-simple-template.png)

在模板中可以填入它的name，parameter name和parameter type。


![fill-in](http://wiki.eclipse.org/images/d/d9/Acceleo-userguide-content-assist-template-2.png)

模板还有一些可选的元素，包括重写、前置条件、后置处理、变量初始化。
![options](http://wiki.eclipse.org/images/d/df/Acceleo-userguide-content-assist-template-3.png)


**Pre-Conditions**


![pre](http://wiki.eclipse.org/images/8/80/Acceleo-userguide-template-precondition.png)

当前置条件语法中的条件满足时，该模板才有作用。


**Post-Treatments**

在实际中将其写入输出文件之前，对模板生成的文本应用某些处理方法通常很有用，特别是对于代码格式化。

例如，修剪模板的结果对于确保格式的同时保持模板的可读格式非常有用。
![trim](http://wiki.eclipse.org/images/4/48/Acceleo-userguide-template-post-treatment.png)
                                                                                                                   
对格式化输出有帮助。

            

**Variable Initialization**


模板在它的主要语义模块中能够定义和初始化变量。


![variables](http://wiki.eclipse.org/images/e/e7/Acceleo-userguide-template-variable-initialization-1.png)

你还能够定义多个变量，可以写成以下形式：

![MULTI	VARIABLES](http://wiki.eclipse.org/images/0/0a/Acceleo-userguide-template-variable-initialization-2.png)



## Queries

Queries用来从模型中提取信息。Queries返回值，或者集合。

可以和Java service 配合使用。

[视频地址](https://youtu.be/wJyPb8VPnFI)

如何创建一个Acceleo模板，并且在原始的一些Java类中有着对应服务的查询。

![java](http://wiki.eclipse.org/images/8/8e/Acceleo-userguide-java-services-5.png)




## Language Elements 语言元素


**File Tags**

文件标签用来告诉Acceleo引擎它必须在真实的文件中生成一些内容，语义如下：

```[file(<uri_expression>, <append_mode>, <output_encoding>)] (...) [/file]	

* <uri_expression> 代表文件的输出名;
* <append_mode> (optional) 是否必须添加到文件中或者替换它的内容
* <output_encoding> (optional)表示用于输出文件的编码。 该编码不需要与模块的编码相同。
```


注意：一个包含```[file]```标签的模板没有必要一定得有一个```@main```，```@main```是用来表示Acceleo eclipse插件必须生成一个java类来封装运行代的所需代码，它可以放置在没有```[file]```标签的模板中，但是这样的模板如果想要得到结果的话最终必须调用一个或多个有```[file]```标签的模板。可以查阅官方的MTL规范获得更多细节。




**For Loops**
Acceleo中的for循环有两种语义表达：

- ```[for (i : E | e)]...[/for] ```
- ``` [for (<iterable_expression>)]...[/for] ```

注意：当使用第二个轻量语义时，会隐含一个变量i，代表着当前迭代的索引值，从1开始。

for循环的一些参数迭代变量名、迭代类型、迭代表达式


还有需要知道描述一些行为的before、between、after每一个循环迭代的可执行功能。



![before..](http://wiki.eclipse.org/images/e/e8/Acceleo-userguide-content-assist-for-4.png)


例如：
```[for (Sequence{1, 2, 3}) before ('sequence: ') separator (', ') after (';')][self/][/for]
```

**If Conditions**

```[if (condition)]...[/if]```


**Let Blocks**

需要知道的是，在Acceleo中的变量是final的，也就直说一旦初始化后是不能更改的，let的语义如下：

```
[let (variableName : VariableType = expression)]...[/let]

```

let中的变量的生命周期不会超过他们的模板，只在let中可见，如果你想要一个子模板来获取一个变量，你可以让它成为一个参数argument。


## Main Modules and Launcher class 主模板和启动类

[main modules](https://youtu.be/r9h9qTy7NdI)

“Main” Acceleo 模板是入口，用以描述整个生成的流程。这种模板可以有普通的模板生成，只需勾上```@main```标记。
他们不需要有一个```[file]```标签： Main模板是可执行模板，他们不必要是那些实际生成文件的模板。


**Behavior**

将使用给定类型在模型的所有元素上调用主模板。 主模板执行的开始初始化了这一代。 因此，如果您在UML类上定义了主模板，则将为所有类调用此模板，但这也意味着每次调用此主模板时都会初始化生成器，例如，缓存 查询将在每次通话后清除。 建议在元模型的根目录下定义主模块，然后在模型中手动导航以调用所有其他模板。 在UML模型中，通过在根概念Model而不是Class上定义生成器，可以提高性能。


























                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    