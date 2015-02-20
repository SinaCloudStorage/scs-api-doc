# SinaImgx使用文档

标签（空格分隔）： 图片处理 imgx 新浪云存储

---

## URL格式：
```
http://imgx.sinacloud.net/<bucket>/<处理指令>/<文件路径>?<querystring>
```
或者
```
http://<bucket>.imgx.sinacloud.net/<处理指令>/<文件路径>?<querystring>
```

## 举例
例如：原始图片在云存储中的url为：http://sinacloud.net/yun/demo/1.jpg
可以看出：

 - bucket为：yun
 - object为：demo/1.jpg
 
下面我们进行下列操作：

 - 裁剪人脸区域, 并将图片缩略：400x400：`c_thumb,g_face,w_400,h_400`
 - 将图片变成圆角，半径为最大(正圆)：`r_max`
 - 将图片亮度增加8%：`e_brightness:8`
 - 将图片格式转换为png格式：`f_png`
 
处理指令为：`c_thumb,g_face,w_400,h_400,r_max,e_brightness:8,f_png`
我们可以通过URL直接进行访问：
```
http://imgx.sinacloud.net/yun/c_thumb,g_face,w_400,h_400,r_max,e_brightness:8,f_png/demo/1.jpg
```
或者
```
http://yun.imgx.sinacloud.net/c_thumb,g_face,w_400,h_400,r_max,e_brightness:8,f_png/demo/1.jpg
```
或者创建一个json文件，内容为：
```json
[
    {  
        "crop" : "thumb",
        "gravity" : "face",
        "width" : 400,
        "height" : 400,
        "radius" : "max",
        "effect" : "brightness:8",
        "format" : "png"
    }
]
```
将文件保存到您对应的bucket下的路径：
```
imgx/t/my_thumb.json    #其中文件名(my_thumb)是您自定义的“指令集名称”，类似css中的class
```
然后就可以通过下面的URL进行访问（也就是说，处理指令也可以不用放在URL中，直接隐藏到您自定义的json文件中）：
```
http://imgx.sinacloud.net/yun/t_my_thumb/demo/1.jpg
```

## 指令格式

- 指令名和指令值之间用”_“连接 `c_fit`
- 相关指令使用逗号隔开“,” `c_fit,w_100,h_100,g_face`
- 多组指令之间用“--”隔开 `c_fit,w_100,h_100,g_face,r_max--l_text:my_font:hello+word,w_100,h_40`

## 签名 & 认证

- 建议您使用URL签名，目的在于您的图片处理指令不会被别人滥用而耗费您的流量，并且可以保护原图不被盗取。
- 注意：签名算法兼容SCS[《签名算法》][1]，`<处理指令>/<文件路径>`作为object参与签名
- 如果您不想使用签名，需要对您的bucket设置一条ACL，对`SINA000000000000IMGX`开放读写权限，可以通过调用API设置，也可以使用控制台设置ACL，如下图：
![][2]
- 如果您不想使用签名，不需要开放匿名权限的ACL，只需要开放`SINA000000000000IMGX`的权限；
- 签名算法参见：[《签名算法》][3] 

## 缓存 & CDN

 - 处理后的图片生成缓存，下次请求不在重复生成，默认缓存7天
 - 处理后的图片会自动推送到CDN节点
 - 如果您的原图修改了，可以使用`v`指令重新生成图片，后面详细介绍
 
## 图片处理指令

<table>
    <thead>
        <tr>
            <th><i>指令名</i></th>
            <th><i>指令</i></th>
		    <th><i>Value</i></th>
		    <th><i>示例</i></th>
		    <th><i>描述</i></th>
        </tr>
        <tr>
            <th width="57px"></th>
            <th width="37px"><b></b></th>
            <th width="125px"><i></i></th>
            <th></th>
            <th></th>
        </tr>
        <tr>
            <th>crop</th>
            <th><b>c</b></th>
            <th><i>mode</i></th>
            <th></th>
            <th>裁剪方式，确定如何将图像裁剪和放缩成所需的尺寸。</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>scale</b></td>
            <td><img src="http://10.13.130.58/yun/c_scale,h_80,w_80/demo/charles.png" /></td>
            <td>改变图像的大小，以匹配给定的宽度和高度。所有原始图像的部分将是可见的，但可能会被拉伸而变形。
                <br /><br /><code>c_scale,h_80,w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>fill</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80/demo/charles.png" /></td>
            <td>裁剪图像，同时保留原有比例。
                <br /><br /><code>c_fill,h_80,w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>lfill</b></td>
            <td><img src="http://10.13.130.58/yun/c_lfill,h_80,w_80/demo/charles.png" /></td>
            <td>同 <i>fill</i> 模式，不同的是限制图片尺寸不大于原图
                <br /><br /><code>c_lfill,h_80,w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>fit</b></td>
            <td><img src="http://10.13.130.58/yun/c_fit,h_80,w_80/demo/charles.png" /></td>
            <td>改变图像的大小，以匹配给定的宽度和高度，同时保留原有比例，所有原始图像的部分将是可见的。等比放缩，不会因为拉伸而变形。
                <br /><br /><code>c_fit,h_80,w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>mfit</b></td>
            <td><img src="http://10.13.130.58/yun/c_mfit,h_80,w_80/demo/charles.png" /></td>
            <td>同 <i>fit</i> 模式，不同的是限制图片尺寸不小于原图
                <br /><br /><code>c_mfit,h_80,w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>limit</b></td>
            <td><img src="http://10.13.130.58/yun/c_limit,h_80,w_80/demo/charles.png" /></td>
            <td>同 <i>fit</i>，不同的是限制图片尺，处理后的图片尺寸不会超过原图。
                <br /><br /><code>c_limit,h_80,w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>pad</b></td>
            <td><img src="http://10.13.130.58/yun/c_pad,h_80,w_80,g_center,b_dddddd/demo/charles.png" /></td>
            <td>指定图像的尺寸，同时保留原有比例。如果原图比例不满足指定的尺寸，将被填充为背景颜色。
                <br /><br /><code>c_pad,h_80,w_80,g_center,b_dddddd</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>lpad</b></td>
            <td><img src="http://10.13.130.58/yun/c_lpad,h_640,w_640,g_center,b_dddddd/demo/charles.png" /></td>
            <td>同 <i>pad</i> 模式，不同的是，如果指定的尺寸大于原图，将不扩大原图
                <br /><br /><code>c_lpad,h_640,w_640,g_center,b_dddddd</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>mpad</b></td>
            <td><img src="http://10.13.130.58/yun/c_mpad,h_80,w_80,g_center,b_dddddd/demo/charles.png" /></td>
            <td>同 <i>pad</i> 模式，不同的是限制图片尺寸不小于原图
                <br /><br /><code>c_mpad,h_80,w_80,g_center,b_dddddd</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>crop</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,h_180,w_180,g_face/demo/charles.png" /></td>
            <td>指定尺寸和位置，用于从原始图片的基础上裁剪出一部分。
                <br /><br /><code>c_crop,h_180,w_180,g_face</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>thumb</b></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,h_110,w_110,g_face/demo/charles.png" /></td>
            <td>定位人脸（结合'face'或'faces'重力参数）并生成缩略图，常用于生成头像。
                <br /><br /><code>c_thumb,h_110,w_110,g_face</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <th></th>
            <th><b></b></th>
            <th><i></i></th>
            <th></th>
            <th></th>
        </tr>
        <tr>
            <th>width</th>
            <th><b>w</b></th>
            <th><i>像素或者百分比</i></th>
            <th></th>
            <th>宽度参数，结合 <i>裁剪方式(crop)</i> 或者 <i>水印(overlay)</i> 使用 </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>80</i></td>
            <td><img src="http://10.13.130.58/yun/w_80/demo/charles.png" /></td>
            <td>调整宽度为80像素
                <br /><br /><code>w_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>0.1</i></td>
            <td><img src="http://10.13.130.58/yun/w_0.1/demo/charles.png" /></td>
            <td>调整图像到其原始尺寸的10％。
                <br /><br /><code>w_0.1</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>height</th>
            <th><b>h</b></th>
            <th><i>像素或者百分比</i></th>
            <th></th>
            <th>高度参数，结合 <i>裁剪方式(crop)</i> 或者 <i>水印(overlay)</i> 使用 </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>80</i></td>
            <td><img src="http://10.13.130.58/yun/h_80/demo/charles.png" /></td>
            <td>调整高度为80像素
                <br /><br /><code>h_80</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>0.1</i></td>
            <td><img src="http://10.13.130.58/yun/h_0.1/demo/charles.png" /></td>
            <td>调整图像到其原始尺寸的10％。
                <br /><br /><code>h_0.1</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <th></th>
            <th><b></b></th>
            <th><i></i></th>
            <th></td>
            <th></th>
        </tr>
        <tr>
            <th>gravity</th>
            <th><b>g</b></th>
            <th><i>用于指定位置或者方向</i></th>
            <th></th>
            <th>1. 用于 <i>'crop', 'pad', 'fill'</i> 的裁剪方式； <br>2. 用于指定 <i>水印(overlay)</i> 的位置</th>
        </tr>
    <thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>north_west</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_north_west,h_200,w_200/demo/4.png" /></td>
            <td>左上位置
                <br /><br /><code>c_crop,g_north_west,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>north</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_north,h_200,w_200/demo/4.png" /></td>
            <td>正上位置，水平方向居中
                <br /><br /><code>c_crop,g_north,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>north_east</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_north_east,h_200,w_200/demo/4.png" /></td>
            <td>右上位置
                <br /><br /><code>c_crop,g_north_east,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>west</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_west,h_200,w_200/demo/4.png" /></td>
            <td>左边，垂直方向居中
                <br /><br /><code>c_crop,g_west,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>center</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_center,h_200,w_200/demo/4.png" /></td>
            <td>正中
                <br /><br /><code>c_crop,g_center,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>east</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_east,h_200,w_200/demo/4.png" /></td>
            <td>右边，垂直方向居中
                <br /><br /><code>c_crop,g_east,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>south_west</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_south_west,h_200,w_200/demo/4.png" /></td>
            <td>左下位置
                <br /><br /><code>c_crop,g_south_west,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>south</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_south,h_200,w_200/demo/4.png" /></td>
            <td>正下位置，水平方向居
                <br /><br /><code>c_crop,g_south,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>south_east</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_south_east,h_200,w_200/demo/4.png" /></td>
            <td>右下位置
                <br /><br /><code>c_crop,g_south_east,h_200,w_200</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>xy_center</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_xy_center,h_400,w_400,x_245,y_240/demo/4.png" /></td>
            <td>指定的x,y坐标，并作为中心点
                <br /><br /><code>c_crop,g_xy_center,h_400,w_400,<br>x_245,y_240</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>face</b></td>
            <td><img src="http://10.13.130.58/yun/c_crop,g_face,h_140,w_140/demo/3.png" /></td>
            <td>自动定位人脸的位置，如果有多张脸，选择最容易识别的一个
                <br /><br /><code>c_crop,g_face,h_140,w_140</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td>face (thumb)</td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_face,h_130,w_140,f_png/demo/1.jpg" /></td>
            <td>自动定位人脸的位置，并且根据指定的尺寸生成缩略图。如果有多张脸，选择最容易识别的一个
                <br /><br /><code>c_thumb,g_face,h_130,w_140,f_png</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>faces</b></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_faces,h_220,w_600,e_brightness:18/demo/3.png" /></td>
            <td>自动定位多张人脸的位置
                <br /><br /><code>c_thumb,g_faces,h_220,w_600,<br>e_brightness:18</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>face:center</b></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_face:center,h_140,w_140/demo/3.png" /></td>
            <td>自动定位人脸的位置，如果找不到人脸则自动定位到原图的中心
                <br /><br /><code>c_thumb,g_face:center,h_140,w_140</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>faces:center</b></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_faces:center,h_120,w_330,e_brightness:18/demo/3.png" /></td>
            <td>自动定位多张人脸的位置，如果找不到人脸则自动定位到原图的中心
                <br /><br /><code>c_thumb,g_faces:center,h_120,w_330<br>,e_brightness:18</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <th></th>
            <th><b></b></th>
            <th><i></i></th>
            <th></th>
            <th></th>
        </tr>
        <tr>
            <th>x</th>
            <th><b>x</b></th>
            <th><i>像素</i></th>
            <th></th>
            <th>用于指定图片裁剪或者水印的横向坐标。</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>110</i></td>
            <td><img src="http://10.13.130.58/yun/c_crop,h_80,w_80,x_110/demo/charles.png" /></td>
            <td>裁剪图像80x80像素，从左边130像素开始
                <br /><br /><code>c_crop,h_80,w_80,x_110</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>y</th>
            <th><b>y</b></th>
            <th><i>像素</i></th>
            <th></th>
            <th>用于指定图片裁剪或者水印的纵向坐标。</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>330</i></td>
            <td><img src="http://10.13.130.58/yun/c_crop,h_80,w_80,x_230,y_330/demo/charles.png" /></td>
            <td>裁剪图像80x80像素，从顶部340像素开始。
                <br /><br /><code>c_crop,h_80,w_80,x_230,y_330</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>quality</th>
            <th><b>q</b></th>
            <th><i>百分比</i></th>
            <th></th>
            <th>控制JPG或者WEBP格式图片的压缩质量。最小值为1，最大值为100。</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>100</i></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_face,h_130,w_140,q_100/demo/1.jpg" /></td>
            <td>图片质量为100%，文件大小为14.3KB。
                <br /><br /><code>c_thumb,g_face,h_130,w_140,q_100</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>10</i></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_face,h_130,w_140,q_10/demo/1.jpg" /></td>
            <td>图片质量为10%，文件大小降低到1.5KB。
                <br /><br /><code>c_thumb,g_face,h_130,w_140,q_10</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>radius</th>
            <th><b>r</b></th>
            <th><i>像素值或者'max'</i></th>
            <th></th>
            <th>指定半径，生成圆角或完全变成圆形（椭圆）。</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>30</i></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_face,h_140,w_140,f_png,r_30/demo/1.jpg" /></td>
            <td>生成30像素半径的圆角
                <br /><br /><code>c_thumb,g_face,h_140,w_140,<br>f_png,r_30</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>max</b></td>
            <td><img src="http://10.13.130.58/yun/c_thumb,g_face,h_140,w_140,f_png,r_max/demo/1.jpg" /></td>
            <td>使用最大半径生成圆角
                <br /><br /><code>c_thumb,g_face,h_140,w_140,<br>f_png,r_max</code></td>
        </tr>
    </tbody>
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>angle</th>
            <th><b>a</b></th>
            <th><i>角度或者翻转模式</i></th>
            <th></th>
            <th>翻转或者旋转图像</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>90</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,a_90/demo/sheep.png" /></td>
            <td>顺时针旋转90度
                <br /><br /><code>c_fill,h_80,w_80,a_90</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>10</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,a_10/demo/sheep.png" /></td>
            <td>顺时针旋转10度
                <br /><br /><code>c_fill,h_80,w_80,a_10</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>-20</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,a_-20/demo/sheep.png" /></td>
            <td>逆时针旋转20度
                <br /><br /><code>c_fill,h_80,w_80,a_-20</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>vflip</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,a_vflip/demo/sheep.png" /></td>
            <td>垂直翻转
                <br /><br /><code>c_fill,h_80,w_80,a_vflip</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>hflip</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,a_hflip/demo/sheep.png" /></td>
            <td>水平翻转
                <br /><br /><code>c_fill,h_80,w_80,a_hflip</code></td>
        </tr>
    </tbody>
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>effect</th>
            <th><b>e</b></th>
            <th><i>name and value</i></th>
            <th></th>
            <th>使用滤镜或者特效</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>grayscale</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_grayscale/demo/horses.png" /></td>
            <td>灰度
                <br /><br /><code>c_fill,h_380,w_380,e_grayscale</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>oil_paint</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_oil_paint/demo/horses.png" /></td>
            <td>油画效果
                <br /><br /><code>c_fill,h_380,w_380,e_oil_paint</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>oil_paint:2</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_oil_paint:2/demo/horses.png" /></td>
            <td>
                使用油画效果，并指定一个level值为2，取值范围1到8，默认值为4
                <br /><br /><code>c_fill,h_380,w_380,e_oil_paint:2</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>negate</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_negate:2/demo/horses.png" /></td>
            <td>
                反色
                <br /><br /><code>c_fill,h_380,w_380,e_negate:2</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>brightness:28</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_brightness:28/demo/horses.png" /></td>
            <td>
                调整图片的亮度，并指定一个百分比值为28，取值范围-100到100，默认值为30
                <br /><br /><code>c_fill,h_380,w_380,e_brightness:28</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>brightness:-28</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_brightness:-20/demo/horses.png" /></td>
            <td>
                调整图片的亮度，并指定一个百分比值为-20，取值范围-100到100，默认值为30
                <br /><br /><code>c_fill,h_380,w_380,e_brightness:-20</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>blur</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_blur/demo/horses.png" /></td>
            <td>
                模糊效果
                <br /><br /><code>c_fill,h_380,w_380,e_blur</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>blur:300</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_blur:300/demo/horses.png" /></td>
            <td>
                使用模糊效果，并指定一个level值为300，取值范围1到2000，默认值为100
                <br /><br /><code>c_fill,h_380,w_380,e_blur:300</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>pixelate</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_pixelate:20/demo/horses.png" /></td>
            <td>
                像素化
                <br /><br /><code>c_fill,h_380,w_380,e_pixelate:20</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>pixelate:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_pixelate:40/demo/horses.png" /></td>
            <td>
                使用像素化效果，并指定一个level值为40，默认值为5
                <br /><br /><code>c_fill,h_380,w_380,e_pixelate:40</code>    
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>sharpen</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_sharpen/demo/horses.png" /></td>
            <td>
                锐化
                <br /><br /><code>c_fill,h_380,w_380,e_sharpen</code>    
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>sharpen:400</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_sharpen:400/demo/horses.png" /></td>
            <td>
                使用锐化效果，并指定一个level值为400，取值范围1到2000，默认值为100
                <br /><br /><code>c_fill,h_380,w_380,e_sharpen:400</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>auto_contrast</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_auto_contrast/demo/horses.png" /></td>
            <td>
                自动对比度
                <br /><br /><code>c_fill,h_380,w_380,e_auto_contrast</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>improve</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_improve/demo/horses.png" /></td>
            <td>
                自动调整图像色彩，对比度和亮度。
                <br /><br /><code>c_fill,h_380,w_380,e_improve</code> 
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><b>sepia</b></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_sepia/demo/horses.png" /></td>
            <td>
                增加褐色，实现老照片效果
                <br /><br /><code>c_fill,h_380,w_380,e_sepia</code>     
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>sepia:60</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_sepia:60/demo/horses.png" /></td>
            <td>
                增加褐色，实现老照片效果，并指定一个level值为60。取值范围1到100，默认值为80。
                <br /><br /><code>c_fill,h_380,w_380,e_sepia:60</code>    
            </td>
            
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>red:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_red:40/demo/horses.png" /></td>
            <td>
                增加红色
                <br /><br /><code>c_fill,h_380,w_380,e_red:40</code>     
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>green:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_green:40/demo/horses.png" /></td>
            <td>
                增加绿色
                <br /><br /><code>c_fill,h_380,w_380,e_green:40</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>blue:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_blue:40/demo/horses.png" /></td>
            <td>
                增加蓝色
                <br /><br /><code>c_fill,h_380,w_380,e_blue:40</code>    
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>yellow:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_yellow:40/demo/horses.png" /></td>
            <td>
                增加黄色
                <br /><br /><code>c_fill,h_380,w_380,e_yellow:40</code>     
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>cyan:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_cyan:40/demo/horses.png" /></td>
            <td>
                增加青色
                <br /><br /><code>c_fill,h_380,w_380,e_cyan:40</code>
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>magenta:40</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_380,w_380,e_magenta:40/demo/horses.png" /></td>
            <td>
                增加粉色
                <br /><br /><code>c_fill,h_380,w_380,e_magenta:40</code>    
            </td>
        </tr>
    </tbody>
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>opacity</th>
            <th><b>o</b></th>
            <th><i>百分比</i></th>
            <th></th>
            <th>控制PNG或者WEBP格式图片不透明度。最小值为1，最大值为100。</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>25</i></td>
            <td><img src="http://10.13.130.58/yun/h_330,w_330,o_25/demo/sheep.png" /></td>
            <td>
                不透明度为25%
                <br /><br /><code>h_330,w_330,o_25</code>      
            </td>
        </tr>
    </tbody>
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>border</th>
            <th><b>bo</b></th>
            <th><i>style</i></th>
            <th></th>
            <th>设置边框</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>10_0000004a</i></td>
            <td><img src="http://10.13.130.58/yun/h_330,w_330,bo_10_0000004a/demo/sheep.png" /></td>
            <td>
                设置一个边框宽度为10px，颜色值为黑色，透明度为4a（16进制）
                <br /><br /><code>h_330,w_330,bo_10_0000004a</code>     
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>8_bbbbbb</i></td>
            <td><img src="http://10.13.130.58/yun/h_330,w_330,bo_8_bbbbbb,r_100/demo/sheep.png" /></td>
            <td>
                对圆角图像设置一个边框宽度为8px，颜色值为bbbbbb
                <br /><br /><code>h_330,w_330,bo_8_bbbbbb,r_100</code>      
            </td>
        </tr>
    </tbody>
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>background</th>
            <th><b>b</b></th>
            <th><i>color</i></th>
            <th></th>
            <th>设置背景颜色，配合其他指令</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>dddddd</i></td>
            <td><img src="http://10.13.130.58/yun/c_pad,w_380,h_180,b_dddddd/demo/charles.png" /></td>
            <td>
                设置背景颜色为dddddd
                <br /><br /><code>c_pad,w_380,h_180,b_dddddd</code>      
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>ff00ff5a</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,a_30,b_ff00ff5a/demo/sheep.png" /></td>
            <td>
                设置背景颜色为ff00ff，透明度为5a (16进制)
                <br /><br /><code>c_fill,h_80,w_80,a_30,b_ff00ff5a</code>    
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>cccccc</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,h_80,w_80,r_max,b_cccccc--c_lpad,g_center,w_100,h_100,b_cccccc/demo/sheep.png" /></td>
            <td>
                设置背景颜色为cccccc
                <br /><br /><code>c_fill,h_80,w_80,r_max,<br />b_cccccc--c_lpad,g_center,<br>w_100,h_100,b_cccccc</code>    
            </td>
        </tr>
    </tbody>
    
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>overlay</th>
            <th><b>l</b></th>
            <th><i>水印图片名，或者字体描述文件名称等</i></th>
            <th></th>
            <th>在原图上增加水印。您可以配合w，h，x，y和重力参数控制水印的尺寸和位置，还可以使用o参数控制水印的透明度</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>vdisk</i></td>
            <td><img src="http://10.13.130.58/yun/c_fill,w_500,h_500,g_face,f_png--l_vdisk,g_south_east,w_250,x_-120,y_-60--l_scs_logo,x_20,y_20/demo/1.jpg" /></td>
            <td>
                在图片的右下角加一个微盘超人的水印（支持外部区域）；首先需要将水印贴图（必须是png格式）保存到您的对应bucket下，路径规则为：imgx/l/my_name.png，然后您可以使用l_my_name指令进行水印操作了
                <br /><br /><code>c_fill,w_500,h_500,g_face,<br>f_png--l_vdisk,g_south_east,<br>w_250,x_-120,y_-60--l_scs_logo,<br>x_20,y_20</code>    
            </td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>text:font_me:你好，云存储</i></td>
            <td><img src="http://10.13.130.58/yun/l_text:font_me:%E4%BD%A0%E5%A5%BD%EF%BC%8C%E4%BA%91%E5%AD%98%E5%82%A8,g_north_west,x_50,y_50/demo/1.jpg" /></td>
            <td>
                文字水印。关于字体样式的设置后面会详细介绍。
                <br /><br /><code>l_text:font_me:<br>%E4%BD%A0%E5%A5%BD<br>%EF%BC%8C%E4%BA%91%<br>E5%AD%98%E5%82%A8,<br>g_north_west,x_50,y_50</code>    
            </td>
        </tr>
    </tbody>
    
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>format</th>
            <th><b>f</b></th>
            <th><i>图片格式</i></th>
            <th></th>
            <th>图片格式转</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>png</i></td>
            <td></td>
            <td><code>f_png</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>webp</i></td>
            <td></td>
            <td><code>f_webp</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>jpeg</i></td>
            <td></td>
            <td><code>f_jpeg</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>jpg</i></td>
            <td></td>
            <td><code>f_jpg</code></td>
        </tr>
    </tbody>
    
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>version</th>
            <th><b>v</b></th>
            <th><i>版本</i></th>
            <th></th>
            <th>缓存未过期的情况下指定版本重新生成图像</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>1.21</i></td>
            <td></td>
            <td>小数 <code>v_1.21</code></td>
        </tr>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>13</i></td>
            <td></td>
            <td>整数 <code>v_13</code></td>
        </tr>
    </tbody>
    
    <thead>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i></i></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <th>transformation</th>
            <th><b>t</b></th>
            <th><i>名称</i></th>
            <th></th>
            <th>"指令集"，指令可以不放到URL中，使用json格式的文件，保存到指定的路径下</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td></td>
            <td><b></b></td>
            <td><i>my_thumbs</i></td>
            <td></td>
            <td>
                自定义名称，在对应的bucket下，创建文件：<i>imgx/t/my_thumbs.json</i>
                <br /><br /><code>t_my_thumbs</code>    
            </td>
        </tr>
    </tbody>
    
</table>

## 水印功能详细介绍

1. 水印分为：`图片水印` 和 `文字水印`；
2. 如果您使用了水印 `l` 指令，则和 `l` 一组的其他指令将针对 `水印图片或者文字` 进行处理（如：`w`、`h`、`g`、`x`、`y`、`o`、`bo`、`e`、`a`、`r`等），后面举例介绍。

### 图片水印

您需要预先将水印贴图保存到对应的bucket下 `imgx/l/<filename>.png`，图片必须是png格式，下面两张图为例：

<table>
    <thead>
        <tr>
		    <th><i>水印贴图</i></th>
		    <th><i>对应路径</i></th>
		    <th><i>对应指令</i></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><img src="http://sinacloud.net/yun/imgx/l/icon_v.png" /></td>
            <td><i>imgx/l/icon_v.png</i></td>
            <td><code>l_icon_v</code></td>
        </tr>
        <tr>
            <td><img src="http://sinacloud.net/yun/imgx/l/scs_logo.png" /></td>
            <td><i>imgx/l/scs_logo.png</i></td>
            <td><code>l_scs_logo</code></td>
        </tr>
    </tbody>
</table>

下面举例介绍具体功能
<table>
    <thead>
        <tr>
		    <th width="300px"><i>示例</i></th>
		    <th><i>描述</i></th>
		    <th><i>指令</i></th>
        </tr>
    </thead>
    <tbody>
        <tr>
		    <td><img src="http://10.13.130.58/yun/c_fit,w_300,f_png--l_scs_logo,g_north_west,w_120,o_25,x_43,y_20,a_-10/demo/1.jpg" /></td>
		    <td>
		        1. 将原图等比放缩；<br>
		        2. 添加一个图片水印到原图的左上角，并且微调坐标(x_43,y_20)，设置水印的宽度为120px，不透明度为25%，逆时针旋转水印10度
		    </td>
		    <td>
		        <code>c_fit,w_300,f_png--<br>l_scs_logo,g_north_west,<br>w_120,o_25,x_43,y_20,a_-10</code>
		    </td>
        </tr>
        <tr>
		    <td><img src="http://10.13.130.58/yun/c_thumb,g_face,w_200,h_200,r_max,bo_6_ffffff80,f_png--l_icon_v,g_south_east,w_60,x_-1,y_-5/demo/1.jpg" /></td>
		    <td>
		        1. 将原图处理成一个圆角头像；<br>
		        2. 在右下角添加一个水印图片，并且向外微调水印坐标(x_-1,y_-5)，设置水印图片的宽度为60px
		    </td>
		    <td>
		        <code>c_thumb,g_face,w_200,h_200,<br>r_max,bo_6_ffffff80,f_png--<br>l_icon_v,g_south_east,w_60,x_-1,y_-5</code>
		    </td>
        </tr>
        <tr>
		    <td><img src="http://10.13.130.58/yun/c_thumb,g_face,w_200,h_200,r_max,bo_6_ffffff80,f_png--l_icon_v,g_south_east,w_60,x_-1,y_-5,e_negate/demo/1.jpg" /></td>
		    <td>
		        1. 同上<br>
		        2. 将水印图片反色
		    </td>
		    <td>
		        <code>c_thumb,g_face,w_200,h_200,<br>r_max,bo_6_ffffff80,f_png--<br>l_icon_v,g_south_east,w_60,x_-1,y_-5<br>,e_negate</code>
		    </td>
        </tr>
    </tbody>
</table>


### 文字水印

您需要预先将文字水印的字体配置(json格式的文件)保存到对应的bucket下 `imgx/l/<file>.json`，例如：
```json
{
    "font_family" : "Xingkai SC",
    "font_style" : "bold",
    "font_size" : 30,
    "font_color" : "000000",
    "background" : "ff0000cc",
    "padding" : 10,
    "word_spacing" : 1,
    "kerning" : 1,
    "line_spacing" : 2,
    "pierced" : false,
    "tile" : false,
    "text" : "默认值",
}
```
对应路径：`imgx/l/my_font.json`

字体参数介绍

*所有参数非必填项*

<table>
    <thead>
        <tr>
		    <th><i>参数名</i></th>
		    <th><i>介绍</i></th>
		    <th><i>默认值</i></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><b>font_family</b></td>
            <td>字体家族，我们支持的所有 <a href="http://10.13.130.58/fonts">字体</a></td>
            <td>Songti SC</td>
        </tr>
        <tr>
            <td><b>font_style</b></td>
            <td>
                字体样式（如果字体本身支持以下样式才有效果，否则默认使用normal）：<br>
                <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;normal（常规）
                <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<i>italic</i>（斜体）
                <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>bold</b>（粗体）
                <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;light（细体）
            </td>
            <td>normal</td>
        </tr>
        <tr>
            <td><b>font_size</b></td>
            <td>字号（单位：px）</td>
            <td>14</td>
        </tr>
        <tr>
            <td><b>font_color</b></td>
            <td>16进制rgba（前6为rgb，最后2位alpha，都是0到f），省略最后两位则认为不透明</td>
            <td>黑色(000000)</td>
        </tr>
        <tr>
            <td><b>background</b></td>
            <td>16进制rgba（前6为rgb，最后2位alpha，都是0到f），省略最后两位则认为不透明</td>
            <td>透明(ffffff00)</td>
        </tr>
        <tr>
            <td><b>padding</b></td>
            <td>文字周围填充的宽度（单位：px）</td>
            <td>6</td>
        </tr>
        <tr>
            <td><b>word_spacing</b></td>
            <td>词间距（单位：px）</td>
            <td>0</td>
        </tr>
        <tr>
            <td><b>kerning</b></td>
            <td>字间距（单位：px）</td>
            <td>0</td>
        </tr>
        <tr>
            <td><b>line_spacing</b></td>
            <td>行间距（单位：px）</td>
            <td>0</td>
        </tr>
        <tr>
            <td><b>pierced</b></td>
            <td>镂空字效果（bool值）</td>
            <td>false</td>
        </tr>
        <tr>
            <td><b>tile</b></td>
            <td>是否平铺（bool值）</td>
            <td>false</td>
        </tr>
        <tr>
            <td><b>text</b></td>
            <td>默认字符串</td>
            <td>空字符串</td>
        </tr>
    </tbody>
</table>


示例1：
`imgx/l/simple_font.json` :
```json
{
    "font_family" : "Microsoft YaHei",
    "font_size" : 40,
    "font_color" : "ffffff"
}
```
一个最简单的文字水印
![](http://10.13.130.58/yun/w_800,f_png--l_text:simple_font:Hello+SCS!!,x_20,y_20,a_-25/demo/1.jpg)

指令：
```
w_800,f_png--l_text:simple_font:Hello+SCS!!,x_20,y_20,a_-25
```

示例2：
`imgx/l/my_font.json` :
```json
{
    "font_family" : "Xingkai SC",
    "font_style" : "bold",
    "font_size" : 40,
    "font_color" : "000000",
    "background" : "ff0000cc",
    "padding" : 18
}
```
过年了，咱们做一个对联：
![](http://10.13.130.58/yun/w_800,f_png--l_text:my_font:%E9%A9%AC%E9%A9%B0%E5%A4%A7%E9%81%93%E5%BE%81%E9%80%94%E8%BF%9C,g_south_west,w_40,x_20,y_40--l_text:my_font:%E7%BE%8A%E4%B8%8A%E5%A5%87%E5%B3%B0%E6%99%AF%E8%89%B2%E5%A8%87,g_south_east,w_40,x_20,y_40--l_text:my_font:%E6%96%B0%E6%B5%AA%E4%BA%91%E5%AD%98%E5%82%A8,g_north,y_20/demo/1.jpg)

指令：
```
w_800,f_png--l_text:my_font:%E9%A9%AC%E9%A9%B0%E5%A4%A7%E9%81%93%E5%BE%81%E9%80%94%E8%BF%9C,g_south_west,w_40,x_20,y_40--l_text:my_font:%E7%BE%8A%E4%B8%8A%E5%A5%87%E5%B3%B0%E6%99%AF%E8%89%B2%E5%A8%87,g_south_east,w_40,x_20,y_40--l_text:my_font:%E6%96%B0%E6%B5%AA%E4%BA%91%E5%AD%98%E5%82%A8,g_north,y_20
```


示例3：
`imgx/l/font_me.json` :
```json
{
    "font_family": "Microsoft YaHei",
    "font_size": 60,
    "font_color": "ffffff",
    "font_style": "bold",
    "background": "0000008f",
    "pierced": true,
    "tile": false,
    "padding": 24,
    "word_spacing": 2.2,
    "line_spacing": 1.2,
    "kerning": 1.5
}
```
镂空字效果
![](http://10.13.130.58/yun/l_text:font_me:%E4%BD%A0%E5%A5%BD%EF%BC%8C%E4%BA%91%E5%AD%98%E5%82%A8,g_west,x_50,y_-100/demo/1.jpg)

指令：
```
l_text:font_me:%E4%BD%A0%E5%A5%BD%EF%BC%8C%E4%BA%91%E5%AD%98%E5%82%A8,g_west,x_50,y_-100
```


示例4：
`imgx/l/tile.json` :
```json
{
    "font_family" : "Microsoft YaHei",
    "font_style" : "bold",
    "font_size" : 40,
    "font_color" : "000000",
    "background" : "ff000066",
    "padding" : 18,
    "tile": true
}
```
平铺效果
![](http://10.13.130.58/yun/w_800,f_png--l_text:tile:Hello+SCS!!,g_south/demo/1.jpg)

指令：
```
w_800,f_png--l_text:tile:Hello+SCS!!,g_south
```


示例5：
`imgx/l/badge.json` :
```json
{
	"font_style" : "bold",
	"font_size" : 30,
	"font_color" : "ffffff",
	"background" : "ff0000cc",
	"padding" : 15
}
```
增加一个badge(数字徽章)
![](http://10.13.130.58/yun/c_thumb,g_face,w_200,h_200,r_max,bo_6_ffffff80,f_png--l_text:badge:69,r_max,g_south_east,w_34,h_34,x_-1,y_-5/demo/1.jpg)

指令：
```
c_thumb,g_face,w_200,h_200,r_max,bo_6_ffffff80,f_png--l_text:badge:69,r_max,g_south_east,w_34,h_34,x_-1,y_-5
```


## 使用“指令集”

如果您不希望把指令暴露在URL中，很简单：

- 先创建一个json文件，保存到对应的bucket中：
路径： `imgx/t/avatar.json` ：
```json
[
    {
        "crop" : "thumb",
        "gravity" : "face",
        "width" : 200,
        "height" : 200,
        "radius" : "max",
        "border" : "6_ffffff80",
        "format" : "png"
    },
    {
        "overlay" : "icon_v",
        "gravity" : "south_east",
        "width" : 60,
        "x" : -1,
        "y" : -5
    }
]


```

- 通过下面的方式访问：

```
http://imgx.sinacloud.net/yun/t_avatar/demo/1.jpg
http://imgx.sinacloud.net/yun/t_avatar/demo/3.png
```

效果：
![](http://10.13.130.58/yun/t_avatar/demo/1.jpg)
![](http://10.13.130.58/yun/t_avatar/demo/3.png)



  [1]: http://open.sinastorage.com/?c=doc&a=guide&section=sign
  [2]: http://yun.sinacloud.net/imgx_acl.png
  [3]: http://open.sinastorage.com/?c=doc&a=guide&section=sign
  [4]: http://sinacloud.net/yun/imgx/l/icon_v.png
  [5]: http://sinacloud.net/yun/imgx/l/scs_logo.png
  [6]: http://10.13.130.58/fonts
