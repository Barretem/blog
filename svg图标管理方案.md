# svg图标管理方案

<a name="3cS8E"></a>
## 背景
目前团队里面用到的图标基本都是**iconfont**，或者直接**本地使用SVG文件**，还有部分是**将图标上传到OSS上面**，然后本地来引用的。目前上面几种方案各有优劣。

方案一：**Iconfont**<br />优点：图标数量繁多，使用方便；<br />缺点：1、**用户权限较难管理**，每次使用图标的时候，都要管理员将用户拉到对应的项目，并且没办法设置用户的删除权限，一旦用户进行图标删除，则后面的使用者处于无限懵逼的状态；2、一般都是以外链的形式引入，那个一般会**比较慢**，会卡页面的显示；

方案二：**本地使用SVG文件**<br />优点：本地使用方便，能够直接引用；打包的文件较小；方便管理；<br />缺点：1、**不便于项目之间的共享**，别的项目根本不清楚有这些图标；2、使用起来的时候不直观，因为svg看上去就是一串代码；

方案三：**使用OSS上面的图标**<br />优点：方便更改（如果部署给客户的话，直接更改远程的图标就可以了，不用更改到本地代码）；本地打包的文件小；<br />缺点：1、OSS上面的文件因为是可以随意更改删除，一旦有人动了这个图标，所有引用者就GG；2、其实方便更改，不用更改本地代码也是个伪命题，因为一旦部署给客户了，一旦改到了这个图标，我们的应用也会被更改掉，最终始终都要更改到代码；3、使用起来的时候不直观，因为所有的图标都放置在一个OSS库里面的话，我们没办法快速找到；

<a name="wUun8"></a>
## 社区常见的方案
以下源于花裤衩的[《手摸手，带你优雅的使用 icon》](https://juejin.im/post/59bb864b5188257e7a427c09)

<a name="Svis5"></a>
### 1. 雪碧图
**远古时代**
在我刚开始实习时，大部分图标都是用 img 来实现的。渐渐发现一个页面的请求资源中图片 img 占了大部分，所以为了优化有了`image sprite` 就是所谓的雪碧图，就是将多个图片合成一个图片，然后利用 css 的 background-position 定位显示不同的 icon 图标。但这个也有一个很大的痛点，维护困难。每新增一个图标，都需要改动原始图片，还可能不小心出错影响到前面定位好的图片，而且一修改雪碧图，图片缓存就失效了，久而久之你不知道该怎么维护了。
<a name="doLWL"></a>
### 2. font库（如Font Awesome）
后来渐渐地一个项目里几乎不会使用任何本地的图片了，而使用一些 font 库来实现页面图标。常见的如 [Font Awesome](https://link.juejin.im?target=http%3A%2F%2Ffontawesome.io%2F) ，使用起来也非常的方便，但它有一个致命的缺点就是找起来真的很不方便，每次找一个图标特别的费眼睛，还有就是它的定制性也非常的不友善，它的图标库一共有675个图标，说少也不少，但还是会常常出现找不到你所需要图标的情况。当然对于没有啥特别 ui 追求的初创公司来说还是能忍一忍的。但随着公司的壮大，来了越来越多对前端指手画脚的人，丧心病狂的设计师，他们会说不！这icon这么丑，这简直是在侮辱他们高级设计师的称号啊！不过好在这时候有了[iconfont](https://link.juejin.im?target=http%3A%2F%2Ficonfont.cn%2F) 。
<a name="hHvRv"></a>
### 3. Iconfont
**iconfont**
一个阿里爸爸做的开源图库，人家还有专门的 [github issue](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fthx%2Ficonfont-plus%2Fissues)(虽然我的一个 issue 半年多了也没回应/(ㄒoㄒ)/~~)，但人家的图标数量还是很惊人的，不仅有几百个公司的开源图标库，还有各式各样的小图标，还支持自定义创建图标库，所以不管你是一家创业公司还是对设计很有要求的公司，它都能很好的帮助你解决管理图标的痛点。你想要的基本都有~。上面的缺点我已经阐述过了，我就不重复介绍了。

<a name="dp6CP"></a>
## 个人想法
需要打造一个自己的图标管理库，这个管理库有以下角色：超级管理员，管理员，用户。超级管理员拥有删除图标库的权限；管理员负责图标上传（系统需要规范图标的大小），图标有对应的分类以及从属项目；用户能够根据项目以及分类来查找到对应的图标，图标只能够下载到项目中进行使用。

<a name="kJHXz"></a>
## 过渡方案
前期首先阿里爸爸的**IconFont来进行图标管理**，图标的使用方式是下**载到对应的项目中进行使用**。<br />

<a name="BvRsZ"></a>
## 项目中的使用方式

<a name="q1Xcf"></a>
### 创建 icon-component 组件
我们有了图标，接下来就是如何在自己的项目中优雅的使用它了。 之后的代码都是基于 vue 的实例(ps: react 也很简单，原理都是类似的)

```html
//components/Icon-svg
<template>
  <svg class="svg-icon" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  name: 'icon-svg',
  props: {
    iconClass: {
      type: String,
      required: true
    }
  },
  computed: {
    iconName() {
      return `#icon-${this.iconClass}`
    }
  }
}
</script>

<style>
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

```javascript
//引入svg组件
import IconSvg from '@/components/IconSvg'

//全局注册icon-svg
Vue.component('icon-svg', IconSvg)

//在代码中使用
<icon-svg icon-class="password" />
```

就这样简单封装了一个 `Icon-svg` 组件 ，我们就可以简单优雅的在自己的vue项目之中使用图标了。

<a name="VOqw6"></a>
### 使用 svg-sprite进一步改造

<a name="OvCo1"></a>
#### 背景
但作为一个有逼格的前端开发，怎能就此满足呢!目前还是有一个致命的缺点的，就是现在所有的 `svg-sprite` 都是通过 iconfont 的 `iconfont.js` 生成的。

- 首先它是一段用js来生成svg的代码，所有图标 icon 都很**不直观**。

![](https://cdn.nlark.com/yuque/0/2019/webp/266079/1563434371729-4bb998cb-7fba-4846-92d3-83b5fff37042.webp#align=left&display=inline&height=352&originHeight=352&originWidth=1280&size=0&status=done&width=1280)
你完全不知道哪个图标名对应什么图标，一脸尼克扬问号??? 每次增删改图标只能整体js文件一起替换。

- 其次它也做不到**按需加载**，不能根据我们使用了那些 svg 动态的生成 `svg-sprite`。
- **自定义性差**，通常导出的svg包含大量的无用信息，例如编辑器源信息、注释等。通常包含其它一些不会影响渲染结果或可以移除的内容。
- **添加不友善**，如果我有一些自定义的svg图标，该如何和原有的 `iconfont` 整合到一起呢？目前只能将其也上传到 `iconfont` 和原有的图标放在一个项目库中，之后再重新下载，很繁琐。
<a name="Wwrw2"></a>
#### 那如何去做呢

接下来我们就要自己来制作 `svg-sprite` 了。这里要使用到 [svg-sprite-loader](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fkisenka%2Fsvg-sprite-loader) 这个神器了， 它是一个 webpack loader ，可以将多个 svg 打包成 `svg-sprite` 。<br />我们来介绍如何在 `vue-cli` 的基础上进行改造，加入 `svg-sprite-loader`。<br />我们发现`vue-cli`默认情况下会使用 `url-loader` 对svg进行处理，会将它放在`/img` 目录下，所以这时候我们引入`svg-sprite-loader` 会引发一些冲突。<br />

```javascript
//默认`vue-cli` 对svg做的处理，正则匹配后缀名为.svg的文件，匹配成功之后使用 url-loader 进行处理。
 {
    test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
    loader: 'url-loader',
    options: {
      limit: 10000,
      name: utils.assetsPath('img/[name].[hash:7].[ext]')
    }
}
```

解决这些冲突的方案有两种，最简单的就是你可以将 test 的 svg 去掉，这样就不会对svg做处理了，当然这样做是很不友善的。

- 你不能保证你所有的 svg 都是用来当做 icon的，有些真的可能只是用来当做图片资源的。
- 不能确保你使用的一些第三方类库会使用到 svg。

所以最安全合理的做法是使用 webpack 的 [exclude](https://link.juejin.im?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fmodule%2F%23rule-exclude) 和 [include](https://link.juejin.im?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fmodule%2F%23rule-include) ，让`svg-sprite-loader`只处理你指定文件夹下面的 svg，`url-loaer`只处理除此文件夹之外的所以 svg，这样就完美解决了之前冲突的问题。
代码如下<br />![](https://cdn.nlark.com/yuque/0/2019/webp/266079/1563434807888-291ad929-0f79-41c0-9ac6-d274a7fbd2f1.webp#align=left&display=inline&height=308&originHeight=308&originWidth=490&size=0&status=done&width=490)<br />这样配置好了，只要引入svg之后填写类名就可以了

```javascript
import '@/src/icons/qq.svg; //引入图标

<svg><use xlink:href="#qq" /></svg>  //使用图标
```
单这样还是非常的不优雅，如果我项目中有一百个 icon，难不成我要手动一个个引入么！ **偷懒是程序员的第一生产力！！！**<br />**
<a name="Gn73z"></a>
### 自动导入svg图标
首先我们创建一个专门放置图标 icon 的文件夹如：`@/src/icons`，将所有 icon 放在这个文件夹下。
之后我们就要使用到 webpack 的 [require.context](https://link.juejin.im?target=https%3A%2F%2Fwebpack.js.org%2Fguides%2Fdependency-management%2F%23require-context)。很多人对于 `require.context`可能比较陌生，直白的解释就是

require.context("./test", false, /.test.js$/);
这行代码就会去 test 文件夹（不包含子目录）下面的找所有文件名以 `.test.js` 结尾的文件能被 require 的文件。
更直白的说就是 我们可以通过正则匹配引入相应的文件模块。


require.context有三个参数：

- directory：说明需要检索的目录
- useSubdirectories：是否检索子目录
- regExp: 匹配文件的正则表达式

了解这些之后，我们就可以这样写来自动引入 `@/src/icons` 下面所有的图标了

```javascript
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```

之后我们增删改图标直接直接文件夹下对应的图标就好了，什么都不用管，就会自动生成 `svg symbol`了。<br />![image.png](https://cdn.nlark.com/yuque/0/2019/png/266079/1563436836495-474f7f20-d10d-4fe8-9f1e-b00be1427f19.png#align=left&display=inline&height=69&name=image.png&originHeight=138&originWidth=1156&size=173719&status=done&width=578)

<a name="Hx5Z2"></a>
## 更进一步优化自己的svg
首先我们来看一下 从 `阿里iconfont` 网站上导出的 svg 长什么样？<br />![](https://cdn.nlark.com/yuque/0/2019/webp/266079/1563436873928-29e1e3ec-7342-4955-bce2-d480e89e0a61.webp#align=left&display=inline&height=313&originHeight=313&originWidth=1280&size=0&status=done&width=1280)<br />没错虽然 iconfont 网站导出的 svg 内容已经算蛮精简的了，但你会发现其实还是与很多无用的信息，造成了不必要的冗余。就连 iconfont 网站导出的 svg 都这样，更不用说那些更在意 ui漂不漂亮不懂技术的设计师了(可能)导出的svg了。好在 `svg-sprite-loader`也考虑到了这点，它目前只会获取 svg 中 path 的内容，而其它的信息一概不会获取。生成 svg 如下图：<br />![](https://cdn.nlark.com/yuque/0/2019/webp/266079/1563436873883-f0038b2d-cdef-4e1a-a465-84ec7154ce77.webp#align=left&display=inline&height=105&originHeight=105&originWidth=1146&size=0&status=done&width=1146)<br />但任何你在 path 中产生的冗余信息它就不会做处理了。如注释什么的<br />![](https://cdn.nlark.com/yuque/0/2019/webp/266079/1563436873932-0a0d40b9-ee49-4251-8a26-ad9e748b35a8.webp#align=left&display=inline&height=251&originHeight=251&originWidth=1280&size=0&status=done&width=1280)<br />这时候我们就要使用另一个很好用的东西了-- [svgo](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fsvg%2Fsvgo)
> SVG files, especially exported from various editors, usually contain a lot of redundant and useless information such as editor metadata, comments, hidden elements, default or non-optimal values and other stuff that can be safely removed or converted without affecting SVG rendering result.

它支持几十种优化项，非常的强大，8k+的star 也足以说明了问题。<br />详细的操作可以参照 [官方文档](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fsvg%2Fsvgo) [张鑫旭大大的文章](https://link.juejin.im?target=http%3A%2F%2Fwww.zhangxinxu.com%2Fwordpress%2F2016%2F02%2Fsvg-compress-tool-svgo-experience%2F)（没错又是这位大大的文章，或许这就是大佬吧！）本文就不展开了。<br />
