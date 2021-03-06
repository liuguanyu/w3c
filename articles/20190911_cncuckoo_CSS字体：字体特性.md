# CSS字体：字体特性

CSS Font Module Level 3（本文以下简称“ML3”）新增了字体特性（Font Feature）属性，用于扩展`font-variant`，以支持丰富OpenType字体特性，如连字。

我们知道，字体文件中包含着从Unicode码点到字形的映射。在给简单的拉丁文本应用字体时，就是根据字体文件中的映射表逐个把对应的字形找到并显示出来。而OpenType和AAT（Apple Advanced Typography）等字体标准支持更复杂的处理模式。比如，给定字符的字形不仅可以根据码点来选择和定位，也可以根据相邻字符或者语言、文字以及文本本身启用的特性来选择和定位。

风格性的字体特性分两类：影响与周围字符关系的和影响字形选择的。前者比如字距调整（kerning）和连字（ligature）相关的特性，后者比如小型大写字母（small-caps）、上/下标（superscript/subscript）及替代（alternate）特性。

为了支持这些字体特性，ML3扩展了`font-variant`，基于它增加了很多子属性用于控制相应的字体特性，比如`font-variant-ligatures`、`font-variant-postion`、`font-variant-caps`、`font-variant-numeric`、`font-variant-east-asian`。而`font-variant`则变成了一个涵盖所有这些属性的简写属性，类似`font`那样。不过，以上这些子属性只会影响字形选择与定位，不影响字体选择。

因为这些字体特性都与风格有关，所以字体特性的英文也叫stylistic font features。这个意义上，unstyled text就不是"未应用样式文本"，而是"未启用风格化字体特性的文本"。

## font-kerning

用于根据字体文件中的数据来调整字形间距。`auto`表示由UA决定，`normal`表示应用字体文件中的数据，`none`表示不应用。

![](https://p5.ssl.qhimg.com/t01d7aeb6816a3b222c.jpg)

对于不包含相关数据的字体，这个属性没有影响。OpenType标准建议默认启用间距调整，即应用OpenType的`kern`特性（对于垂直文本则是`vkrn`）。如果同时定义了`letter-spacing`属性，则字体内置的间距作为默认值，在此基础上再进行调整。

## font-variant-ligatures

用于跟上下文构形（contextual form）一起组合字形以得到更和谐的文本呈现效果。

- `normal`表示启用公共默认特性，对OpenType字体则意味着默认启用普通/标准（common）连字和上下文构形，禁用自由（discretionary）连字和传统（historical）连字。

- `none`表示明确禁用任何形式的连字和上下文构形。

- `common-ligatures`启用普通/标准连字（对应OpenType特性`liga`、`clig`）。对于OpenType字体，普通/标准连字默认是启用的。

  ![](https://p1.ssl.qhimg.com/t01562d70aefb8ea014.jpg)

- `no-common-ligtures`禁用普通/标准连字。

- `discretionary-ligatures`启用自由连字（对应OpenType特性`dlig`），这种连字是自由的或者说可选的，由字体设计者自行决定。至于哪些地方会连字，需要参考字体文档。

  ![](https://p2.ssl.qhimg.com/t015c6156b5cf6ab982.jpg)

- `no-discretionary-ligatures`禁用自由连字。

- `historical-ligatures`启用传统连字（对应OpenType特性`hlig`）。

  ![](https://p1.ssl.qhimg.com/t01625422176661e9f9.jpg)

- `no-historical-ligatures`禁用传统连字。

- `contextual`启用上下文替代（contextual alternates，对应OpenType特性`calt`），虽然不是严格意义上连字特性，但这个特性常用于让字形与周围内容更和文谐。对于OpenFont字体，这个特性默认打开。

  ![](https://p3.ssl.qhimg.com/t01e7e8e01648e9e62f.jpg)

- `no-contextual`禁用上下文替代。

为了正确渲染复杂文字而必需的连字（对应OpenType特性`rlig`），不受以上设置的影响，包括`none`。

## font-variant-position

用于在排版中使用上标和下标字形。这些字形是单独设计的，但它们的字框与默认字形是一样的。目的就是无需缩放或调整基线，就可以像使用默认字形一样直接使用它们。从下图可以看出下标字形（上）与合成下标（下）的区别：

![](https://p4.ssl.qhimg.com/t01201ba49c81a613f9.jpg)

- `normal`不应用下面的特性。
- `sub`显示下标变体（OpenType特性`subs`）。
- `super`显示上标变体（OpenType特性`sups`）。

如果字体文件中没有相应的变体，UA需要基于正常字形缩小后合成。这两个值不支持嵌套应用（即不累积），另外对行内图片应用这两个值，效果如同应用`normal`。

考虑到以前UA会通过给`sub`和`sup`元素设置相应的`font-size`和`vertical-align`属性来模拟下标和上标，样式表作者最好通过CSS3条件来应用`font-variant-position`属性，以实现向后兼容。

比如，UA通过对`sub`元素应用以下样式：

```css
sub {
  vertical-align: sub;
  font-size: smaller;
  line-height: normal;
}
```

而这样使用`font-varian-position`则可以保证不支持用户代理也能正常显示下标：

```css
@supports ( font-variant-position: sub ) {

  sub {
    vertical-align: baseline;
    font-size: 100%;
    line-height: inherit;
    font-variant-position: sub;
  }

}
```

## font-varian-caps

这个属性用于从字体中选择专门设计的小型大写字母字形。

- `normal`不应用下面的特性。

- `small-caps`用小型大写字母显示小写字母（OpenType特性`smcp`）。小型大写字母是小写字母大小的大写字母，通常比x高度高30%，或者是大写字母高度的70%。

  ![](https://p5.ssl.qhimg.com/t01b8dd0299ae4ff79d.jpg)

- `all-small-caps`大写字母和小写字母都显示为小型大写字母（OpenType特性`c2sc`、`smcp`）。

  ![](https://p4.ssl.qhimg.com/t01acc1f4ac85f6c4be.jpg)

- `petite-caps`用短身（petite）大写字母显示小写字母（OpenType特性`pcap`）。短身大写字母的高度等于x高度。

  ![](https://p3.ssl.qhimg.com/t019b6dd450b75b18fd.jpg)

- `all-petite-caps`大写字母和小写字母都显示为短身大写字母（OpenType特性`c2pc`、`pcap`）。

  ![](https://p3.ssl.qhimg.com/t015187a73dafffd3ee.jpg)

- `unicase`大写字母转换为小型大写字母与小写字母混合显示（OpenType特性`unic`）。

  ![](https://p5.ssl.qhimg.com/t017c14e9351334776b.jpg)

- `titling-caps`用标题大写字母显示小写字母（OpenType特性`titl`）。

  ![](https://p4.ssl.qhimg.com/t01716a71aa5ed2ff68.jpg)

如果字体不支持`small-caps`，UA需要用大写字母合成。如果字体不支持`petite-caps`，则`petite-caps`和`all-petite-caps`相当于指定`small-caps`和`all-small-caps`。如果字体不支持`unicase`，则该属性就像`small-caps`只应用给大写字母。如果字体本身不支持`titling-caps`，则视觉上无变化。对于不区分大小的文字，`small-caps`、`all-small-caps`、`petite-caps`、`all-petite-caps`和`unicase`视觉上无变化。

在模拟小型大写字母时，结果必须与`text-transform`属性的结果保持一致。

## font-variant-numeric

控制如何显示数字值。

- `normal`不应用下面的特性。

- `lining-nums`等高数字（OpenType特性`lnum`）。

  ![](https://p3.ssl.qhimg.com/t017da9269c1fc7deaf.jpg)

- `oldstyle-nums`旧样式数字（OpenType特性`onum`），高度可能不相等。

  ![](https://p2.ssl.qhimg.com/t015a9b411bf11ba6aa.jpg)

- `proportional-nums`比例数字（OpenType特性`pnum`），宽度可能不相等。

  ![](https://p1.ssl.qhimg.com/t017164540b35fcc5c6.jpg)

- `tabular-nums`等宽数字（OpenType特性`tnum`）。

  ![](https://p2.ssl.qhimg.com/t0182d8f915765e4fef.jpg)

- `diagonal-fractions`斜角分数字（OpenType特性`frac`）。

  ![](https://p3.ssl.qhimg.com/t013012cb41aca2341a.jpg)

- `stacked-fractions`标准分数字（OpenType特性`afrc`）

  ![](https://p0.ssl.qhimg.com/t014d208b93a49d7bb4.jpg)

- `ordinal`序数字（OpenType特性`ordn`）

  ![](https://p2.ssl.qhimg.com/t016aa9890460f9ac59.jpg)

- `slashed-zero`带斜线的零（OpenType特性`zero`）。

  ![](https://p0.ssl.qhimg.com/t01c98622badd804270.jpg)



对于`ordinal`来说，虽然序数也以上标形式出现，但标记不同。对于上标，通常这样标记和应用样式：

```html
sup { font-variant-position: super; }
x<sup>2</sup>
```

而对于序数，则这样标记和应用样式：

```html
.ordinal { font-variant-numeric: ordinal; }
<span class="ordinal">17th</span>
```

这里的`th`会变成序数形式，而数字不变。当然，在有些语言里，序数也不是上标形式。

## font-variant-east-asian

控制东亚文字的替代及大小。

- `normal`不应用以下特性。

- `jis78`将默认日文替换成JIS78中对应的字形（OpenType特性`jp78`）。

  ![](https://p2.ssl.qhimg.com/t01474af99d6db8e11c.jpg)

- `jis83`将默认日文替换成JIS83中对应的字形（OpenType特性`jp83`）。

- `jis90`将日文替换成JIS90中对应的字形（OpenType特性`jp90`）。

- `jis04`使用JIS2004中的日文字形（OpenType特性`jp04`）。

  以上JIS变体主要对应不同版本的日语国际标准。字体通常按照最新国际标准设计，但显示时偶尔需要使用旧的变体，比如匹配标志。

- `simplified`应用简体形式（OpenType特性`smpl`）。

- `traditional`应用繁体形式（OpenType特性`trad`）。

  ![](https://p5.ssl.qhimg.com/t01e75cce532ece841f.jpg)

  `simplified`和`traditional`用于控制文字以简化写法还是传统写法显示。

- `full-width`应用全宽变体（OpenType特性`fwid`）。

  ![](https://p2.ssl.qhimg.com/t013716c9da0af86911.jpg)

- `proportional-width`应用比例间距变体（OpenType特性`pwid`）。

  ![](https://p5.ssl.qhimg.com/t0155ebd874e844a497.jpg)

- `ruby`应用注音/注释变体（OpenType特性`ruby`）。由于注音/注释文本通常比较小，字体设计者通常特别设计相应字形。

  ![](https://p0.ssl.qhimg.com/t01cc0887556297516e.jpg)

## font-variant

如前所述，`font-variant`是所有字体变体子属性的简写形式。它的值`normal`会将所有子属性的值重置为初始值。`none`将`font-variant-ligatures`设置为`none`，将其他特性设置为各自的初始值。与其他简写属性一样，`font-variant`会将未指定的子属性重置为各自的初始值。

`font-variant`不会重置`font-feature-settings`属性。

## font-feature-settings

这是用于控制OpenType字体特性的一个低级属性。这个属性旨在访问那些使用不广泛但有时候却必要的特性。

> 正常情况下，应该使用`font-variant`及其子属性。只有在那些属性不够用的情况下，再使用`font-feature-settings`。
>

```
/* 应用小型大写字母和第二个花饰字替代 */
font-feature-settings: "smcp", "swsh" 2;
```

`normal`值表示字形选择和定位不会发生变化。特性值标签的格式如下：

```css
<feature-tag-value> = <string> [ <integer> | on | off ]?
```

`<string>`是区分大小写的OpenType特性标签，由4个小写字母构成。不存在的标签会被忽略。UA不会合成字体本身不支持的特性。唯一例外是`kern`；如果字体的`kern`表中有字距调整数据，而GPOS表里显示不支持`kern`特性，UA也会合成。

`<integer>`如果存在，表示用于字形选择的索引，必须大于等于0。0表示禁用相应特性。对于布尔值特性，1表示启用该特性。对于非布尔值特性，大于等于1的值表示启用该特性，以及该特性的选择索引。值`on`相当于1，值`off`相当于0。如果没有指定这个部分的值，则假定值为1。

`font-feature-settings `的计算属性是一个映射，因此不存在重复指定的值。如果存在多次指定同一属性的值，则最后一个覆盖前面的。

```css
font-feature-settings: "dlig" 1;       /* dlig=1 启用自由连字 */
font-feature-settings: "smcp" on;      /* smcp=1 启用小型大写 */
font-feature-settings: 'c2sc';         /* c2sc=1 全部小型大写 */
font-feature-settings: "liga" off;     /* liga=0 禁用标准连字 */
font-feature-settings: "tnum", 'hist'; /* tnum=1, hist=1 启用表列数字及传统连字 */
font-feature-settings: "tnum" "hist";  /* 无效，缺少逗号 */
font-feature-settings: "silly" off;    /* 无效，标签过长 */
font-feature-settings: "PKRN";         /* PKRN=1 启用自定义特性 */
font-feature-settings: dlig;           /* 无效，标签不是字符串 */
```

虽然特性标签是为OpenType字体的特性打造的，但其他现代字体格式的特性也可以以OpenType标签的模式来注册。

## 小结

综观所有`font-variant`的子属性，视觉上可以看得到的，好像只有`font-variant-east-asian `与汉字有关（比如`traditional`把简体转换成繁体，`ruby`选择注音/注释字形）。当然，`font-variant-ligatures`也可能与汉字有关，如果行、草字体如果有连字替换设计的话。

虽然大多数属性都与字母文字相关，但在国际化的今天，了解这些文字的展示也是有益的。

## 参考链接

- CSS Fonts Module Level 3：<https://www.w3.org/TR/css-fonts-3/>
- The Complete CSS Demo for OpenType Features：https://sparanoid.com/lab/opentype-features
- Syntax for OpenType features in CSS：https://helpx.adobe.com/fonts/using/open-type-syntax.html
- An Introduction to OpenType Substitution Features：https://ilovetypography.com/OpenType/opentype-features.html
- CSS Conditional Rules Module Level 3：https://www.w3.org/TR/2013/CR-css3-conditional-20130404/
- OpenType sepc - Feature tags：https://docs.microsoft.com/zh-cn/typography/opentype/spec/featuretags



