---
title: Maven问题和解决方案记录
categories: 工具
---

### 一、无脑配镜像

```Java
// tmd啥镜像都不用配
```

### 二、删除lastupdated文件

```Java
public static void listDirectory(File dir) throws IOException {
    if (!dir.exists())
        throw new IllegalArgumentException("目录：" + dir + "不存在.");
    if (!dir.isDirectory()) {
        throw new IllegalArgumentException(dir + " 不是目录。");
    }
    File[] files = dir.listFiles();
    if (files != null && files.length > 0) {
        for (File file : files) {
            if (file.isDirectory())
                //递归
                listDirectory(file);
            else{ // 删除以 lastUpdated 结尾的文件
                String fileName = file.getName();
                boolean isLastupdated = fileName.toLowerCase().endsWith("lastupdated");
                if (isLastupdated){
                    boolean is_delete = file.delete();
                    System.out.println("删除的文件名 => " + file.getName() + "  || 是否删除成功？ ==> " + is_delete);
                }
            }
        }
    }
}

public static void main(String[] args) throws IOException {
    // 指定maven的本地仓库
    listDirectory(new File("E:\\programs\\mvnrepository"));
}
```

### 三、问题

##### 描述1：

> Could not transfer artifact xxx from/to xxx解决方案

##### 解决方案1：

1. 用上述工具类删除下载失败的文件
2. 在`pom.xml`文件中，右键->Maven->reload project
3. 完美运行

##### 解决方案2：

删除文件不用多说

然后在终端输入以下命令

```bash
mvn clean && mvn compile -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true
```

然后刷新试试（注意，maven没有配置镜像，裸连）

---

##### 描述2：

出现SSL证书问题

##### 解决方案2：

最简单粗暴的，在终端输入

```bash
mvn clean && mvn compile -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true
```

如果显示**无法识别mvn命令**，那就是**环境变量**没配置对，重新去配下maven的环境变量

[剩余方法参考这里](https://blog.csdn.net/qq_22041375/article/details/103491941)

