
原文地址：[https://www.smashingmagazine.com/category/browsers](https://www.smashingmagazine.com/category/browsers)

你知道未使用的CSS对性能的影响吗？剧透：太多了！在本文中，我们将探索一种面向Sass的解决方案，用于处理未使用的CSS，避免需要涉及无头浏览器和DOM仿真的复杂Node.js依赖项。

在现代前端开发中，开发人员应该致力于编写可扩展且可维护的CSS。否则，随着代码库的增长和更多开发人员的贡献，他们可能会失去对级联和选择器特性等细节的控制。

实现这一点的一种方法是使用面向对象的 CSS (OOCSS)等方法，这种方法不是围绕页面上下文组织 CSS，而是鼓励将结构(网格系统、间距、宽度等)与装饰(字体、品牌、颜色等)分离。

所以 CSS 类的名称如下:

* .blog-right-column
* .latest_topics_list
* .job-vacancy-ad

替换为更多可重用的替代品，这些替代品应用相同的 CSS 样式，但不绑定到任何特定的上下文:

* .col-md-4
* .list-group
* .card

   这种方法通常在 Sass 框架(例如 Bootstrap、 Foundation)的帮助下实现，或者越来越多地使用定制框架(定制框架) ，这种框架可以更好地适应项目。

所以现在我们使用的 CSS 类是从模式、 UI 组件和实用程序类的框架中精心挑选出来的。 下面的例子演示了一个使用 Bootstrap 构建的通用网格系统，它垂直堆叠，然后一旦到达 md 断点，就切换到3列布局。

```html
<div class="container">
   <div class="row">
      <div class="col-12 col-md-4">Column 1</div>
      <div class="col-12 col-md-4">Column 2</div>
      <div class="col-12 col-md-4">Column 3</div>
   </div>
</div>
```

这里使用以编程方式生成的类(例如.col-12和.col-md-4)来创建此模式。但是.col-1到.col-11，.col-lg-4，.col-md-6或.col-sm-12呢？这些都是将包含在编译的CSS样式表中的类的示例，浏览器下载并解析这些类，尽管它们不在使用中。

   在本文中，我们将首先探讨未使用的 CSS 对页面加载速度的影响。 然后，我们将介绍一些从样式表中删除它的现有解决方案，并继续使用我自己的面向 sass 的解决方案。

# 测量未使用的 CSS 类的影响
   虽然我喜欢谢菲尔德联队，很有实力，他们的网站的 CSS 是压缩成一个单一的568kb 的小文件，即使 压缩至105kb。 看起来也很多。
  ![sheffield-united-website](/content/images/2019/08/sheffield-united-website.jpg)
>[这是Sheffield United’s website 谢菲尔德联队的网站](https://www.sufc.co.uk/)，我当地的足球队.
 
我们可以看看他们的主页上实际使用了多少CSS吗？在Google上快速搜索就会发现很多在线工具，但我更喜欢使用Chrome中的Coverage工具，它可以直接从Chrome的DevTools上运行。让我们试一试。
 
 ![unused-css](/content/images/2019/08/unused-css.png)
> 访问开发工具中覆盖工具的最快方法是使用快捷键控件 + Shift + p 或命令 + Shift + p (Mac)来打开命令菜单。 在其中，键入 Coverage，并选择“ Show Coverage”选项。
 
   结果显示，主页只使用568kb 样式表中30kb 的 CSS，其余538kb 用于网站其余部分所需的样式。 这意味着高达94.8% 的 CSS 没有被使用。
   
 ![waiting-css](/content/images/2019/08/waiting-css.png)
 
   Css 是网页关键渲染路径的一部分，它包含了浏览器在开始页面渲染之前必须完成的所有不同步骤。 这使得 CSS 成为一个渲染阻塞资产。
   
  因此，考虑到这一点，当使用良好的3g连接谢菲尔德联队的网站时，在下载CSS和开始页面渲染之前，需要整个1.15秒的时间。这是个问题。
    
   谷歌也意识到了这一点。 当运行 Lighthouse 审计时，在线或通过浏览器，任何潜在的加载时间和文件大小节省可以通过删除未使用的 CSS 突出显示。
   
![lighthouse](/content/images/2019/08/lighthouse.png)
> 在 Chrome (和 Chromium Edge)中，你可以通过点击开发工具中的 Audit 选项卡来右键 Google Lighthouse 审计。
> 
# 现有的解决方案
我们的目标是确定哪些CSS类是不需要的，并从样式表中删除它们。 现有的解决方案试图使这个过程自动化。 它们通常可以通过 Node.js 的构建脚本使用，或者通过任务运行器(如Gulp)使用。 其中包括:

* [UNCSS](https://github.com/uncss/uncss)
* [PurifyCSS](https://github.com/purifycss/purifycss)
* [PurgeCSS](https://www.purgecss.com/)

这些通常以类似的方式工作:

1.在 bulld 上，可以通过无头浏览器(如: puppeter)或 DOM 仿真(如: jsdom)访问该网站。
2.基于页面的 HTML 元素，任何未使用的 CSS 都会被识别出来。
3.这将从样式表中删除，只留下所需的内容。

虽然这些自动化工具非常有效，而且我已经成功地在许多商业项目中使用了其中的许多工具，但是在这个过程中我遇到了一些值得分享的缺点:

* 如果类名包含特殊字符，如“@”或“/” ，那么如果不编写一些自定义代码，可能无法识别这些字符。 我使用 Harry Roberts 设计的 BEM-IT，它涉及到用响应后缀构造类名，比如: u-width-6/12@lg，所以我以前碰到过这个问题。
* 如果网站使用自动部署，它可以放慢构建过程，特别是如果你有很多页面和很多 CSS。
* 需要在团队中共享有关这些工具的知识，否则当 CSS 在生产样式表中神秘地缺失时，可能会出现混乱和沮丧。
* 如果你的网站有很多第三方脚本在运行，有时在无头浏览器中打开时，这些脚本不能很好地运行，并且可能在过滤过程中导致错误。 因此，当检测到无头浏览器时，通常你必须编写自定义代码来排除任何第三方脚本，这取决于你的设置，可能很棘手。
* 通常，这些类型的工具都很复杂，并且给构建过程引入了许多额外的依赖项。 与所有第三方依赖一样，这意味着依赖其他人的代码。

带着这些观点，我向自己提出了一个问题:

> 仅仅使用 Sass,是否有更好的方式处理我们编译的sass，达到排除未使用的CSS目的，而不需要直接粗暴地删除sass中的源类呢？

剧透一下: 我想答案是肯定的。

# 面向 Sass 的解决方案

这个解决方案需要提供一种快速、简单的方法来挑选 Sass 应该编译的内容，同时又足够简单，不会给开发过程增加任何复杂性，也不会阻止开发人员利用诸如编程生成的 CSS 类之类的东西。

首先，有一个带有构建脚本和一些样例样式的 repo，您可以从[这里](https://github.com/WebDevLuke/handlingunusedcss/tree/start)克隆它们。

提示: 如果遇到问题，总是可以与 [master分支](https://github.com/WebDevLuke/handlingunusedcss/tree/master)上已完成的版本交叉引用。

cd到repo，运行npm install，然后npm运行build，根据需要将所有Sass文件编译CSS。这应该会在 dist目录中创建一个55kb的css文件。

如果你在浏览器中打开/dist/index.html，你会看到一个相当标准的组件，点击它就会展开显示一些内容。 您也可以在这里查看，这里将应用实际的网络条件，因此您可以运行自己的测试。

![expander-component-1](/content/images/2019/08/expander-component-1.png)

> 在开发面向`sass`的解决方案来处理未使用的CSS时，我们将使用此扩展 UI 组件作为测试主题
# 部分级别进行过滤

在一个典型的 SCSS 设置中，您可能只有一个清单文件(例如: repo 中的 main.SCSS) ，或者每页有一个清单文件(例如: index.SCSS、 products.SCSS、 contact.SCSS) ，其中导入框架部分。 根据 OOCSS的原则，这些入口可能看起来如下:

例一：

``` css (sass)
/*
Undecorated design patterns
*/

@import 'objects/box';
@import 'objects/container';
@import 'objects/layout';

/*
UI components
*/

@import 'components/button';
@import 'components/expander';
@import 'components/typography';

/*
Highly specific helper classes
*/

@import 'utilities/alignments';
@import 'utilities/widths';

```
如果其中任何一个部分没有被使用，那么过滤这个未使用的 CSS 的自然方式就是禁用导入，这将阻止它被编译。

例二：

``` css
/*
Undecorated design patterns
*/
// @import 'objects/box';
// @import 'objects/container';
// @import 'objects/layout';

/*
UI components
*/
// @import 'components/button';
@import 'components/expander';
// @import 'components/typography';

/*
Highly specific helper classes
*/
// @import 'utilities/alignments';
// @import 'utilities/widths';
```

然而，正如OOCSS一样，我们将装饰与结构分离，以便最大限度地实现可重用性，因此扩展程序可能需要来自其他对象、组件或实用程序类的CSS才能正确地呈现。除非开发人员通过检查HTML意识到这些关系,否则他们可能不知道导入这些部分，因此不会编译所有必需的类。

在 repo中，如果查看`dist/index.html`中扩展程序的 HTML，就会发现情况是这样的。 它使用来自框和布局对象、排版组件以及宽度和对齐实用工具的样式。

### dist/index.html
``` html
<div class="c-expander">
   <div class="o-box o-box--spacing-small c-expander__trigger c-expander__header" tabindex="0">
      <div class="o-layout o-layout--fit u-flex-middle">
         <div class="o-layout__item u-width-grow">
            <h2 class="c-type-echo">Toggle Expander</h2>
         </div>
         <div class="o-layout__item u-width-shrink">
            <div class="c-expander__header-icon"></div>
         </div>
      </div>
   </div>
   <div class="c-expander__content">
      <div class="o-box o-box--spacing-small">
       Lorum ipsum
         <p class="u-align-center">
            <button class="c-expander__trigger c-button">Close</button>
         </p>
      </div>
   </div>
</div>
```

让我们通过将这些关系正式化到Sass本身来解决这个问题，因此一旦导入了一个组件，任何依赖关系也将自动导入。通过这种方式，开发人员不再需要审计HTML来了解需要导入的其他内容。

# 程序化导入映射
为了让这个依赖系统工作，而不是简单地在清单文件中的@import 语句中进行注释，Sass 逻辑将需要规定是否部分编译。

在`src/scss/settings`中，创建一个名为`imports.scss`的新部分,@import它到`settings/core.scss`中，然后创建以下SCSS映射:
**src/scss/settings/_core.scss**
``` css
@import 'breakpoints';
@import 'spacing';
@import 'imports';
```
**src/scss/settings/_imports.scss**
``` css
$imports: (
   object: (
    'box',
    'container',
    'layout'
   ),
   component: (
    'button',
    'expander',
    'typography'
   ),
   utility: (
    'alignments',
    'widths'
   )
);
```
此映射将具有与示例一中的导入清单相同的角色。

例四：

``` css
$imports: (
   object: (
    //'box',
    //'container',
    //'layout'
   ),
   component: (
    //'button',
    'expander',
    //'typography'
   ),
   utility: (
    //'alignments',
    //'widths'
   )
);
```
它的行为应该类似于标准的@imports集，因为如果某些部分被注释掉(如上所述) ，那么该代码就不应该在构建时编译。
但是当我们想自动导入依赖项时，我们也应该能够在正确的情况下忽略这个映射。

# 渲染混合
让我们开始添加一些 Sass 逻辑。在`src/scss/tools`中创建`render.scss`，然后将其@import 添加到`tools/core.scss`中。
在该文件中，创建一个名为 render ()的空 mixin。

**src/scss/tools/_render.scss**
``` css
@mixin render() {

}
```
在 mixin中，我们需要编写Sass，它执行以下操作:
* render()
    嘿，有$import，天气很好，不是吗？比如说，你的地图中有容器对象吗？
* $imports 
    false
* render()
    “太可惜了，看起来那时候就不会编了。按钮组件怎么样？“
* $imports
    true
* render()
    “很好！ 这就是正在编译的按钮。 替我向妻子问好。
    
在Sass中，这转换为以下内容：

**src/scss/tools/_render.scss**
``` css
@mixin render($name, $layer) {
   @if(index(map-get($imports, $layer), $name)) {
      @content;
   }
}
```
基本上，检查部分内容是否包含在 $imports 变量中，如果包含，使用 Sass'@content 指令呈现它，这允许我们向 mixin 传递一个内容块。

我们可以这样使用它:

例五:

``` css
@include render('button', 'component') {
   .c-button {
      // styles et al
   }

   // any other class declarations
}
```

在使用这个 mixin 之前，我们可以对它做一个小的改进。 层名称(对象、组件、实用程序等)是我们可以安全预测的东西，因此我们有机会稍微简化一下。

在渲染 mixin 声明之前，创建一个名为 $layer 的变量，并从 mixin 参数中删除名称相同的变量。 像这样:

**src/scss/tools/_render.scss**
``` css
$layer: null !default;
@mixin render($name) {
   @if(index(map-get($imports, $layer), $name)) {
      @content;
   }
}
```
现在，在对象、组件和 utility@imports 所在的`core.scss`部分中，将这些变量重新声明为以下值; 表示要导入的 CSS 类的类型。

**src/scss/objects/_core.scss**
``` css
$layer: 'object';

@import 'box';
@import 'container';
@import 'layout';
```

**src/scss/components/_core.scss**
``` css
$layer: 'component';

@import 'button';
@import 'expander';
@import 'typography';
```
**src/scss/utilities/_core.scss**
``` css
$layer: 'utility';

@import 'alignments';
@import 'widths';
```

这样，当我们使用 render () mixin 时，我们要做的就是声明部分名称。

按照下面的步骤，将 render () mixin 包围在每个对象、组件和实用程序类声明周围。 这将为您提供一个渲染混合使用每部分。

例如：

**src/scss/objects/_layout.scss**
``` css
@include render('button') {
   .c-button {
      // styles et al
   }

   // any other class declarations
}
```
 
**src/scss/components/_button.scss**
``` css
@include render('button') {
   .c-button {
      // styles et al
   }

   // any other class declarations
}
```

注意: 对于 `utilities/widths.scss`，在编译时将 render()函数包装在整个部分中会出错，因为在 Sass 中，不能在 mixin 调用中嵌套 mixin 声明。 相反，只需将render() mixin 包裹在 create-widths()调用周围，如下所示:

``` css
@include render('widths') {

// GENERATE STANDARD WIDTHS
//---------------------------------------------------------------------

// Example: .u-width-1/3
@include create-widths($utility-widths-sets);

// GENERATE RESPONSIVE WIDTHS
//---------------------------------------------------------------------

// Create responsive variants using settings.breakpoints
// Changes width when breakpoint is hit
// Example: .u-width-1/3@md

@each $bp-name, $bp-value in $mq-breakpoints {
   @include mq(#{$bp-name}) {
      @include create-widths($utility-widths-sets, \@, #{$bp-name});
   }
}

// End render
}
```

有了这个，在构建时，只有 $imports 中引用的部分将被编译。

混合并匹配 $imports 中注释掉的组件，并在终端中运行 npm 运行 build 来尝试一下。

# 依存关系图

现在我们通过程序导入部分，我们可以开始实现依赖逻辑。
  
在 `src/scss/settings` 中，创建一个名为 `dependencies.scss` 的新部分,将它@import到`settings/core.scss`，但要确保它在 imports.scss 之后。 然后在其中创建如下SCSS映射:
**src/scss/settings/_dependencies.scss**
``` css
$dependencies: (
   expander: (
      object: (
         'box',
         'layout'
    ),
    component: (
         'button',
         'typography'
    ),
    utility: (
         'alignments',
         'widths'
    )
   )
);
```

在这里，我们声明了expander组件的依赖项，因为它需要来自其他分区的样式才能正确呈现，如dist/index.html 所示。

使用这个列表，我们可以编写逻辑，这意味着无论 $imports 变量的状态如何，这些依赖项总是与它们的依赖组件一起被编译。

在 $dependencies 下面，创建一个名为 dependency-setup ()的 mixin。 在这里，我们将执行以下操作:

**1. 循环遍历依赖关系映射**
``` css
@mixin dependency-setup() {
   @each $componentKey, $componentValue in $dependencies {
    
   }
}
```
**2.  如果可以在 $imports 中找到该组件，则循环遍历其依赖项列表。**
``` css
@mixin dependency-setup() {
   $components: map-get($imports, component);
   @each $componentKey, $componentValue in $dependencies {
      @if(index($components, $componentKey)) {
         @each $layerKey, $layerValue in $componentValue {

         }
      }
   }
}
```
**3.  如果依赖项不在 $imports 中，那么添加它。**

``` css
@mixin dependency-setup() {
   $components: map-get($imports, component);
   @each $componentKey, $componentValue in $dependencies {
       @if(index($components, $componentKey)) {
           @each $layerKey, $layerValue in $componentValue {
               @each $partKey, $partValue in $layerValue {
                   @if not index(map-get($imports, $layerKey), $partKey) {
                       $imports: map-merge($imports, (
                           $layerKey: append(map-get($imports,  $layerKey), '#{$partKey}')
                       )) !global;
                   }
               }
           }
       }
   }
}
```
**4.  那么就只需要调用 mixin 了**
``` css
@mixin dependency-setup() {
   ...
}
@include dependency-setup();
```

所以我们现在有的是一个增强的部分导入系统，如果一个组件被导入，开发人员不必手动导入它的各种依赖项部分。 

配置 $imports变量，只导入expander 组件，然后运行 npm 运行 build。 您应该可以在编译后的 CSS中看到 expander 类及其所有依赖项。

然而，就过滤掉未使用的 CSS 而言，这并没有带来任何新的东西，因为相同数量的 Sass 仍然被导入，不管是否是编程的。 让我们改进一下。

# 改进的依赖性导入
一个组件可能只需要一个来自依赖项的类，因此继续并导入该依赖项的所有类只会导致我们试图避免的同样不必要的膨胀。 

我们可以对系统进行改进，允许对每个类进行更细粒度的过滤，以确保组件只使用它们所需的依赖类进行编译。 

对于大多数设计模式，无论是否经过修饰，样式表中都存在一定数量的类，这样样式才能正确显示。 

对于使用诸如 BEM 之类的已有变数命名原则的类名，通常最少需要使用“Block”和“ Element”命名类，而“modifier”通常是可选的。

> 注意: 实用类通常不会遵循 BEM 路线，因为它们本质上是独立的，因为它们的关注点很窄。

例如，看看这个media 对象，它可能是最著名的面向对象的 CSS 例子:

``` html
<div class="o-media o-media--spacing-small">
   <div class="o-media__image">
     <img src="url" alt="Image">
   </div>
   <div class="o-media__text">
      Oh!
   </div>
</div>
```

如果组件将此设置为依赖项，则始终编译.o-media、.o-media_image和.o-media_text是有意义的，因为这是使模式工作所需的最小CSS数量。然而，在.o-media-Spacing-small是可选修饰符的情况下，应该仅在我们明确这样说的情况下才能编译它，因为它的用法可能不会在所有媒体对象实例中都是一致的。

我们将修改 $dependencies映射的结构，以允许我们导入这些可选类，同时包括一种只导入块和元素的方法，以防不需要修饰符。

首先，检查`dist/index.html`中的 expder html，并记录所有正在使用的依赖类。 将它们记录在 $dependencies 映射中，如下所示:

**src/scss/settings/_dependencies.scss**
``` css
$dependencies: (
   expander: (
       object: (
           box: (
               'o-box--spacing-small'
           ),
           layout: (
               'o-layout--fit'
           )
       ),
       component: (
           button: true,
           typography: (
               'c-type-echo',
           )
       ),
       utility: (
           alignments: (
               'u-flex-middle',
               'u-align-center'
           ),
           widths: (
               'u-width-grow',
               'u-width-shrink'
           )
       )
   )
);
```

当一个值设置为 true 时，我们将其转换为“只编译块和元素级别的类，没有修饰符! ” .

下一步包括创建一个白名单变量来存储这些类，以及我们希望手动导入的任何其他(非依赖性)类。在/src/scss/settings/imports.scss中，在$Imports之后，创建一个名为$global-filter的新Sass列表。

**src/scss/settings/_imports.scss**
``` css
$global-filter: ();
```

$global-filter 背后的基本前提是，存储在这里的任何类都将在 build 上编译，只要它们所属的部分类是通过 $import 导入的。

如果它们是一个组件依赖项，那么这些类名可以通过编程方式添加，或者可以在声明变量时手动添加，如下面的例子所示:

#### 全局过滤器示例

``` css
$global-filter: (
   'o-box--spacing-regular@md',
   'u-align-center',
   'u-width-6/12@lg'
);
```

接下来，我们需要向@dependency-setup mixin 添加更多的逻辑，这样 $dependencies 中引用的任何类都会自动添加到我们的 $global-filter 白名单中。

下面这个区块:

**src/scss/settings/_dependencies.scss**
``` css
@if not index(map-get($imports, $layerKey), $partKey) {

}
```

... 添加下面的片段。
**src/scss/settings/_dependencies.scss**
``` css
@each $class in $partValue {
   $global-filter: append($global-filter, '#{$class}', 'comma') !global;
}
```

这将遍历任何依赖类，并将它们添加到 $global-filter 白名单中。

此时，如果在 dependency-setup () mixin 下面添加@debug 语句，以便在终端中打印出 $global-filter 的内容:

``` css
@debug $global-filter; 
```

...你应该在 build 上看到这样的东西:

> DEBUG: "o-box--spacing-small", "o-layout--fit", "c-box--rounded", "true", "true", "u-flex-middle", "u-align-center", "u-width-grow", "u-width-shrink"

现在我们有了一个类白名单，我们需要在所有不同的对象、组件和实用程序部分中强制执行它。

在src/scss/tools中创建名为_filter.scss的新部分，并将@import添加到Tools层的_core.scss文件中。

在这个新的部分中，我们将创建一个名为filter()的混合。我们将使用它来应用逻辑，这意味着只有包含在$global-filter变量中的类才会被编译。

从简单开始，创建一个Mixin，它接受单个参数-过滤器控制的$class。接下来，如果$class包含在$global-filter白名单中，则允许对其进行编译。

**src/scss/tools/_filter.scss**
``` css
@mixin filter($class) {
   @if(index($global-filter, $class)) {
      @content;
   }
}
```
在 partial 中，我们将 mixin 包装在一个可选类中，如下所示:
``` css
@include filter('o-myobject--modifier') {
   .o-myobject--modifier {
      color: yellow;
   }
}
```

这意味着。 O-myobject -- modifier 类只有包含在 $global-filter 中才会被编译，$global-filter 可以直接设置，也可以通过 $dependencies 设置间接设置。

浏览 repo 并将 filter () mixin 应用到对象和组件层之间的所有可选修饰符类。 

当处理排版组件或实用工具层时，因为每个类都是独立于下一个类的，所以让它们都是可选的是有意义的，所以我们可以根据需要启用类。 这里有几个例子:

**src/scss/objects/_layout.scss**
``` css
@include filter('o-layout__item--fit-height') {
    .o-layout__item--fit-height {
        align-self: stretch;
    }
}
```

**src/scss/utilities/_alignments.scss**
``` css
// Changes alignment when breakpoint is hit
// Example: .u-align-left@md
@each $bp-name, $bp-value in $mq-breakpoints {
    @include mq(#{$bp-name}) {
        @include filter('u-align-left@#{$bp-name}') {
            .u-align-left\@#{$bp-name} {
                text-align: left !important;
            }
        }

        @include filter('u-align-center@#{$bp-name}') {
            .u-align-center\@#{$bp-name} {
                text-align: center !important;
            }
        }

        @include filter('u-align-right@#{$bp-name}') {
            .u-align-right\@#{$bp-name} {
                text-align: right !important;
            }
        }
    }
}
```

> 注意：当将响应后缀类名添加到filter()混合中时，您不必用‘\’转义‘@’符号。

在此过程中，当将filter()Mixin应用于部分值时，您可能(或可能没有)注意到一些事情。

#### 分组类
代码库中的一些类被分组在一起，并且共享相同的样式，例如：

**src/scss/objects/_box.scss**
``` css
.o-box--spacing-disable-left,
.o-box--spacing-horizontal {
    padding-left: 0;
}
```

由于过滤器只接受单个类，因此它没有考虑到一个样式声明块可能用于多个类的可能性。 

为了解决这个问题，我们将扩展 filter () mixin，这样除了一个类之外，它还可以接受包含许多类的 Sass 符号列表。 像这样:

**src/scss/objects/_box.scss**
``` css
@include filter('o-box--spacing-disable-left', 'o-box--spacing-horizontal') {
    .o-box--spacing-disable-left,
    .o-box--spacing-horizontal {
        padding-left: 0;
    }
}
```

因此，我们需要告诉 filter () mixin，如果这些类中的任何一个在 $global-filter 中，则允许编译这些类。

这将涉及额外的逻辑来检查 mixin 的 $class 参数，如果传递一个arglist来检查每个条目是否在 $global-filter 变量中，则使用循环来响应。

**src/scss/tools/_filter.scss**
``` css
@mixin filter($class...) {
    @if(type-of($class) == 'arglist') {
        @each $item in $class {
            @if(index($global-filter, $item)) {
                @content;
            }
        }
    }
    @else if(index($global-filter, $class)) {
        @content;
    }
}
```

然后只需要返回以下部分以正确应用 filter () mixin:
* objects/_box.scss
* objects/_layout.scss
* utilities/_alignments.scss

此时，回到 $import 并只启用 expander 组件。 在编译后的样式表中，除了来自通用层和元素层的样式之外，您应该只看到以下内容:
* 属于 expander 组件的块和元素类，但不是它的修饰符。 
* 属于扩展程序的依赖项的块和元素类。 
* 在 $dependencies 变量中显式声明的属于扩展程序依赖项的任何修饰符类。

从理论上讲，如果您决定要在编译的样式表中包含更多的类，比如 expander components modifier，那么只需要在声明处将其添加到 $global-filter 变量，或者将其附加到代码库中的其他位置(只要在声明修饰符本身之前)。

#### 启用一切

因此我们现在有了一个相当完整的系统，它允许您导入对象、组件和实用程序到这些部分中的单个类。 

在开发过程中，不管出于什么原因，您可能只希望一次性启用所有内容。 为了实现这一点，我们将创建一个名为 $enable-all-classes 的新变量，然后添加一些额外的逻辑，这样，如果将其设置为 true，那么无论 $imports 和 $global-filter 变量的状态如何，都会编译所有的内容。

首先，在我们的主清单文件中声明变量:

**src/scss/main.scss**
``` css
$enable-all-classes: false;

@import 'settings/core';
@import 'tools/core';
@import 'generic/core';
@import 'elements/core';
@import 'objects/core';
@import 'components/core';
@import 'utilities/core';
```

然后，我们只需要对 filter ()和 render () mixin 进行一些小的编辑，以便在`$enable-all-classes` 变量设置为 true 时添加一些覆盖逻辑。 

首先，过滤器()混合。 在进行任何现有检查之前，我们将添加一个`@if` 语句，以查看 `$enable-all-classes` 是否设置为 true，如果设置为true，则呈现@content，不询问任何问题。

**src/scss/tools/_filter.scss**
``` css
@mixin filter($class...) {
    @if($enable-all-classes) {
        @content;
    }
    @else if(type-of($class) == 'arglist') {
        @each $item in $class {
            @if(index($global-filter, $item)) {
                @content;
            }
        }
    }
    @else if(index($global-filter, $class)) {
        @content;
    }
}
```

接下来在 render () mixin 中，我们只需要检查 $enable-all-classes 变量是否是真实的，如果是，则跳过任何进一步的检查。

**src/scss/tools/_render.scss**
``` css
$layer: null !default;
@mixin render($name) {
    @if($enable-all-classes or index(map-get($imports, $layer), $name)) {
        @content;
    }
}
```

所以现在，如果您将 $enable-all-classes 变量设置为 true 并重新生成，那么每个可选类都会被编译，从而在这个过程中节省了相当多的时间。

#### 比较

为了了解这种技术给我们带来了什么样的好处，让我们进行一些比较，看看文件大小的差异。 

为了确保比较公平，我们应该在 $import 中添加 box 和 container 对象，然后在 $global-filter 中添加 box 的 o-box-spacing-regular 修饰符，如下所示:


**src/scss/settings/_imports.scss**

``` css
$imports: (
     object: (
        'box',
        'container'
        // 'layout'
     ),
     component: (
        // 'button',
        'expander'
        // 'typography'
     ),
     utility: (
        // 'alignments',
        // 'widths'
     )
);

$global-filter: (
    'o-box--spacing-regular'
);
```

这样可以确保扩展程序的父元素的样式按照没有进行过滤的情况下的样式进行编译。

#### 原始样式与过滤样式
让我们比较一下原始样式表和已编译的所有类，以及只编译了扩展器组件所需的 CSS 的已过滤样式表。

**Standard**
Stylesheet	| Size (kb)	 | Size (gzip)
---|:--:|---:
[Original](https://webdevluke.github.io/handlingunusedcss/dist/index2.html) |54.6kb | 6.98kb
[Filtered](https://webdevluke.github.io/handlingunusedcss/dist/index.html) | 15.34kb (72% smaller) |4.91kb (29% smaller)

您可能认为 gzip 百分比的节省意味着不值得这样做，因为原始样式表和过滤样式表之间没有太大差别。 

值得强调的是，gzip 压缩可以更好地处理更大、更重复的文件。 因为经过过滤的样式表是唯一的概念验证，并且只包含用于 expander 组件的 CSS，所以不需要像在实际项目中那样进行大量压缩。 

如果我们将每个样式表按10倍的比例放大到网站的 CSS 捆绑包大小的典型尺寸，gzip 文件大小的差异会更加令人印象深刻。

**10x Size**
Stylesheet	| Size (kb) | Size (gzip)
---|:--:|---:
Original (10x) | 892.07kb | 75.70kb
Filtered (10x) | 209.45kb (77% smaller) | 19.47kb (74% smaller)

#### 过滤样式表 VS UNCSS

下面是过滤样式表和通过 UNCSS 工具运行的样式表之间的比较。

**Filtered vs UNCSS**
Stylesheet	| Size (kb) | Size (gzip)
---|:--:|---:
Filtered | 15.34kb | 4.91kb
UNCSS | 12.89kb (16% smaller) | 4.25kb (13% smaller)

Uncss 工具在这方面略胜一筹，因为它过滤掉了通用目录和元素目录中的 CSS。 

在一个真实的网站上，如果使用的 HTML 元素种类较多，那么这两种方法之间的区别可能微不足道。

#### 包装

因此，我们已经看到，只使用 Sass，就可以更好地控制在 build 上编译哪些 CSS 类。 这样可以减少最终样式表中未使用的 CSS 数量，并加快关键呈现路径。 

在本文开头，我列举了 UNCSS 等现有解决方案的一些缺点。 以同样的方式批评这个面向 sass 的解决方案是公平的，所以在你决定哪种方法更适合你之前，所有的事实都摆在桌面上:

**PROS**
* 不需要额外的依赖项，因此您不必依赖其他人的代码。 
* 比基于 Node.js 的替代方案需要更少的构建时间，因为你不需要运行 headless 浏览器来审计你的代码。 
* 这对于持续集成特别有用，因为您可能不太可能看到构建队列。 结果在类似的文件大小相比，自动化工具。 
* 开箱即用，您可以完全控制正在筛选的代码，无论代码中如何使用这些 CSS 类。 使用基于 Node.js 的替代品，你经常需要维护一个单独的白名单，这样属于动态注入 HTML 的 CSS 类就不会被过滤掉。

**CONS**
* 面向 sass 的解决方案肯定更加实用，因为您必须掌握 $import 和 $global-filter 变量。 除了最初的设置，我们看到的 Node.js 的替代品大部分都是自动化的。
* 如果您将 CSS 类添加到 $global-filter，然后从 HTML 中删除它们，那么您需要记住更新变量，否则您将编译不需要的 CSS。 由于大型项目是由多个开发人员在任何时候工作，这可能不容易管理，除非你适当地计划它。
* 我不建议将这个系统绑定到任何现有的 CSS 代码库中，因为您必须花费相当多的时间将依赖项拼接起来，并将 render()混合模式应用到许多类中。 在没有现有代码的情况下，使用新构建可以更容易地实现这个系统。

*水平有限，如有不妥之处，欢迎指正!*
