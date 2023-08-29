---
title: JavaFx笔记
tags: [JavaFx]
---

## JavaFx环境配置

1、下载JavaFx的SDK[JavaFx中文网]([JavaFX - Gluon (gluonhq.com)](https://gluonhq.com/products/javafx/))，并解压

2、IDEA创建新的Java项目，然后将解压后的JavaFx的SDK中的lib目录下所有的Jar包复制到项目下

![jar包](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304212116568.png)

3、在IDEA中，选择文件 -> 项目结构 -> 库，然后把SDK中所有的Jar包选中

![添加](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304212120989.png)

4、然后创建一个类，加入以下代码。

```java
public class JavaFxMain extends Application {
    @Override
    public void start(Stage primaryStage) throws Exception {
        primaryStage.setTitle("这是我的第一个JavaFx程序");
        primaryStage.setMaxHeight(400);
        primaryStage.setMaxWidth(400);
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch();
    }
}
```

5、进行启动配置，在程序实参部分加入以下代码，其中**D:\Path\JavaFxSdk\openjfx-17.0.7_windows-x64_bin-sdk\javafx-sdk-17.0.7\lib**部分即下载的SDK的存储位置，一定要到lib目录，配置完成后，重新添加应用的运行环境即可。

```shell
--module-path "D:\Path\JavaFxSdk\openjfx-17.0.7_windows-x64_bin-sdk\javafx-sdk-17.0.7\lib" --add-modules javafx.controls,javafx.fxml
```



![配置](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304212128601.png)

6、完成上述步骤后，点击运行，出现白色的窗口即说明配置完成。

![运行](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304212132114.png)

## 概述

JavaFx整体包括**Stages 舞台** **Scenes 场景** **Scene Graphs 布局** 以及控件(布局之中才是控件)

![架构](https://cdn.nlark.com/yuque/0/2022/png/23145762/1650784177227-de6afb5c-7190-4e3e-9a24-a899ef74d504.png?x-oss-process=image%2Fresize%2Cw_382%2Climit_0)

### 场景、布局、控件简单示例

```java
public class JavaFxMain extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception {
        //舞台设置标题
        primaryStage.setTitle("测试javaFx");
        //布局
        HBox hBox = new HBox();
        //控件
        Button button = new Button("登录");
        //将控件添加到布局中
        hBox.getChildren().add(button);

        //场景
        Scene scene = new Scene(hBox, 450, 250);
        //将场景添加到舞台中
        primaryStage.setScene(scene);
        //展示舞台
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch();
    }
}
```

效果

![效果](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304212153337.png)



​	其他笔记

[在线文档]([JavaFX 概述 (yuque.com)](https://www.yuque.com/chengxuyuanyideng/wmiqe2/kl9uvp))



## 舞台







