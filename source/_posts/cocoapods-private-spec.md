---
title: cocoapods创建私有化仓库
date: 2018-10-14 00:00:33
tags: 组件化
author: 彭凡
---
摘要:
cocoapods创建私有化仓库
<!-- more -->

##### 前言
在开发过程中，不管是封装、继承、多态，都是解决代码重用的不同解决方案，而组件化也是为了这个目的，将一个业务、一个功能、甚至一段代码组件化，以达到复用的目的。 功能解耦也可以说是贯穿整个编程发展的任务，为了代码复用的精准，自然也就要求其组件化出去的代码能够解耦. 而采用组件化的最主要操作就是创建自己的私有仓库.下面介绍一下利用cocoapods创建私有化仓库的过程.


##### 创建私有代码仓库和spec仓库
###### 1.创建私有代码仓库
 Github创建私有仓库是需要收费的,所以这里我们选择第三方代码托管平台(GitLab,coding.net等).这里我采用的是coding.net来做自己的代码托管.首先我创建一个托管当前组件的私有代码仓库, 然后在桌面创建一个TestLib文件夹,cd,到这个文件夹,把仓库克隆下来.

[私有代码仓库地址: https://git.coding.net/pengfan1/TestLib.git
]( [https://git.coding.net/pengfan1/TestLib.git]
)

克隆好了之后,我们可以把模块代码拖到~/Desktop/TestLib/TestLib中
然后把代码push上去,这样,代码仓库的准备工作就做好了

###### 2. 创建私有spec仓库
下面创建spec仓库, 这时候你可能会问,不是已经有了代码仓库吗,为什么还要创建spec仓库.这里说明一下,code repository是代码仓库，我们把包代码上传到这个仓库,spec repository是配置仓库，所有的配置按照包名、版本号分门别类的存放在这个仓库。这个仓库只用来存放spec文件，不存放代码。

[私有spec仓库地址: https://git.coding.net/pengfan1/TestLibSpec.git
]( [https://git.coding.net/pengfan1/TestLibSpec.git]
)

##### 搭建环境
###### 创建podspec文件
找到本地仓库的路径,然后利用如下命令创建你podspec文件
> pod spec create TestLib

创建这个文件后，需要修改里面的一些东西，例如这里我用TestLib私有仓库做例子。这里面修改的东西，表示你的私有仓库的一些配置，例如名称,代码源,版本之类的.

需要注意的是，下面的source设置为TestLib仓库而不是Spec仓库，这一步很多人都在这里犯错。

~~~ objc
Pod::Spec.new do |s|
s.name          = "TestLib"
s.version       = "0.0.1"
s.summary       = "description of TestLib"
s.description   = <<-DESC
"this is TestLib content description."
DESC
s.homepage      = "https://git.coding.net/pengfan1/TestLib.git
"
s.license       = "MIT"
s.author        = { "pengfan" => "17600380167@163.com" }
s.source        = { :git => "https://git.coding.net/pengfan1/TestLib.git
", :tag => "#{s.version}" }
s.source_files  = "TestLib/*.{h,m}"
s.platform      = :ios
end

~~~

创建好之后用如下命令验证一下podspec文件(在podspec文件所在的目录下)
> pod lib lint
如果没问题,控制台会输出 TestLib passed validation


###### 关联podspec到spec私有仓库
通过下面命令,添加远程Spec仓库到本地,相当于建立一个连接关系.

> pod repo add TestLib https://git.coding.net/pengfan1/TestLibSpec.git

添加完成后, 用以下命令看一下安装在本地的spec 仓库的列表
 > pod repo list

 这时候你就会看到安装在本地的TestLib的spec仓库
 cd 到 这个spec仓库的本地文件目录下

 用如下命令验证一下spec仓库
> pod repo lint .

###### 为TestLib添加tag
在上面创建podspec文件的时候你设置了version为0.0.1,那么代码仓库应该有这个对应的tag.
cd到~/Desktop/TestLib/TestLib文件夹,用如下命令创建tag,并提交代码
>  git tag '0.0.1'
> git push origin --tags

提交代码
> git add .
> git commit -m "提交tag"
> git push origin master


###### 提交podspec文件到远程spec仓库
通过如下命令,我们将podspec文件push到远程的spec仓库.
> pod repo push TestLib TestLib.podspec


##### 使用私有仓库代码
这个时候,开发团队的其他成员就可以通过cocoapods来导入你编写的组件了. 在podfile文件中添加如下代码:

~~~ objc
source 'https://git.coding.net/pengfan1/TestLibSpec.git'
source 'https://github.com/CocoaPods/Specs.git'

platform :ios , '8.0'

target 'MainProject' do
#第三方库
pod 'Masonry'
pod 'MGJRouter'

#私有仓库
pod 'TestLib',   '~> 0.0.1'
end

~~~

在配置好这套环境后，之后的开发就只需提交代码,然后执行pod repo push的操作。根据业务需求发布指定的组件版本，并将对应版本的podspec文件push到Spec私有仓库即可。

版本控制也非常简单，只需要在Podfile中指定某个私有仓库的版本号即可。


##### 遇到的问题
1.podspec文件配置问题
如果配置podspec文件的source_files不正确的时候,会报错:
~~~ -objc
"The `source_files` pattern did not match any file."
~~~

我的项目文件结构如下图:
![TestLib文件夹](https://raw.githubusercontent.com/pengfan123/images/master/TestLib.png)

![TestLib子文件夹](https://raw.githubusercontent.com/pengfan123/images/master/TestLib-sub.png)

这里我配置的是
~~~ -objc
s.source_files  = "TestLib/*.{h,m}"
~~~
首先,起头是与podspec文件同级的TestLib文件夹,然后匹配的是TestLib文件夹下.h,.m后缀的文件,这里不会匹配TestLib文件夹下子文件夹中的.h,.m文件

2.项目中同时用到公有pod和私有pod的时候,podfile中一定要配置公有spec源和私有spec源,顺序无所谓,例如上面的:

~~~ -objc
source 'https://git.coding.net/pengfan1/TestLibSpec.git'
source 'https://github.com/CocoaPods/Specs.git'

platform :ios , '8.0'

target 'MainProject' do
#第三方库
pod 'Masonry'
pod 'MGJRouter'

#私有仓库
pod 'TestLib',   '~> 0.0.1'
end
~~~

3.在发布自己私有库新版本时,一定要在提交了代码到自己的私有代码仓库之后再提交tag,因为tag对应的是一个release版本,正常的逻辑就是先提交代码再Release.

4.pod install 和 pod update的区别  

pod install 一般是你第一次想要为项目添加pod的时候使用的，它同样也使用在你为Podfile文件添加或移除pod库的时候。每次pod install命令运行的时候，pod install会为每一个它安装的pod库在Podfile.lock文件中写入其版本号。Podfile.lock文件追踪每一个安装的pod库的版本号，并锁定这些版本号。

pod update:
当你运行了pod update PODNAME命令，CocoaPods会在不考虑Podfile.lock中版本的情况下试着去查找PODNAME的最新版本。pod update PODNAME命令会将相应的pod更新到最新的版本（新版本仍然遵守你在Podfile中做的限制）
