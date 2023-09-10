## 一、Rront-matter

### 1. Page Front-matter

![01](imgs/01.png)



### 2. Post Front-matter

![02](imgs/02.png)



## 二、Special MD-Syntax

### 1. 标签外挂

#### 1）Note

- 语法

  ```markdown
  {% note [class] [no-icon] [style] %}
  Any content (support inline tags too.io).
  {% endnote %}
  ```

  - `class`【可选】：不同的表示有不同的配色（`default`、`primary`、`success`、`info`、`warning`、`danger`），不填即为`default`
  - `no-icon`【可选】：不显示 `icon`
  - `style`【可选】：已在配置文件中配置过（`simple`、`modern`、`flat`、`disabled`）

- 效果

  ![03](imgs/03.png)



#### 2）Tabs

- 语法

  ```markdown
  {% tabs Unique name, [index] %}
  <!-- tab [Tab caption] [@icon] -->
  Any content (support inline tags too).
  <!-- endtab -->
  {% endtabs %}
  ```

  - `Unique name`【必填】：标识该 Tabs
  - `index`【可选】：决定使用哪个 tab 作为默认显示，正常值为正整数，-1 表示不默认显示任何一个 tab
  - `Tab caption`【可选】：显示在 tab 上的文字
  - `@icon`【可选】：显示在 tab 上文字之前的图标

- 效果

  ![04](imgs/04.png)



#### 3）label

- 语法

  ```markdown
  {% label text color %}
  ```

  - `text`【必填】：文字
  - `color`【可选】：背景颜色，默认为`default`。（`default`、`blue`、`pink`、`red`、`purple`、`orange`、`green`）

- 效果

  ![05](imgs/05.png)

