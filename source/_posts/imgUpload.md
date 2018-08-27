---
title: 移动端-图片压缩上传实践
date: 2018-08-27
photo: https://shuangmuyingzi.github.io/img/npm.jpg
---

本文主要讲解移动端的图片压缩上传，在移动端压缩图片并且上传主要用到filereader、canvas 以及 formdata 这三个h5的api。

<!--more-->

在做移动端图片上传的时候，用户传的都是手机本地图片，而本地图片一般都相对比较大，拿iphone6来说，平时拍很多图片都是一两M的，如果直接这样上传，那图片就太大了，如果用户用的是移动流量，完全把图片上传显然不是一个好办法。

　　目前来说，HTML5的各种新API都在移动端的webkit上得到了较好的实现。根据查看caniuse，本demo里使用到的FileReader、Blob、Formdata对象均已在大部分移动设备浏览器中得到了实现（safari6.0+、android 3.0+），所以直接在前端压缩图片，已经成了很多移动端图片上传的必备功能了。

　　在移动端压缩图片并且上传主要用到filereader、canvas 以及 formdata 这三个h5的api。逻辑并不难。整个过程就是：

　　（1）用户使用input file上传图片的时候，用filereader读取用户上传的图片数据（base64格式）

　　（2）把图片数据传入img对象，然后将img绘制到canvas上，再调用canvas.toDataURL对图片进行压缩

　　（3）获取到压缩后的base64格式图片数据，转成二进制塞入formdata，再通过XmlHttpRequest提交formdata。

　　如此三步，就完成了图片的压缩和上传。

　　说起来好像挺简单，其实还是有些坑的。接下来就直接用代码进行分析：
　　
### 获取图片数据
先是获取图片数据，也就是监听input file的change事件，然后获取到上传的文件对象files，如果是多张图需将类数组的files转成数组，然后进行forEach遍历。

　　接着判断文件类型，如果不是图片则不作处理。如果是图片就实例化一个filereader，以base64格式读取上传的文件数据，判断数据长度，如果大于3M的图片就调用compress方法进行压缩，否则调用upload方法进行上传。　

```
    // 上传图片回显到本地并且判断超过3M走压缩否则直接上传到服务器
    choosePhoto:function(event){
                
                var that = this;
				var target = event.currentTarget;
                var file = target.files[0];
                
                // 判断图片的类型大小
                var imgType = /\.jpg|\.png|\.jpeg/i;
                if( !imgType.test(file.name) ){
                    this.layer("只支持图片上传，请选择正确的图片格式");
                    return;
                }
                // 利用fileReader 预览图片
                if( typeof FileReader =="underfined" )  {
                    this.layer("您的浏览器不支持图片预览功能，请换浏览器重试");
                
                }else{
                    var reader = new FileReader();
                    reader.onload = function(){
                        that.img.src = reader.result;
        					var img = new Image();
        				img.src = reader.result;
						if((file.size/1024000)>3){
							var data = that.compress(img);
							that.Upload2(data,file.type,idx);
						}else{
							that.Upload(target.files[0],idx);
						} 
                        // input清空，防止两次请求同一个时不能选中
                        target.value = '';
					}
					reader.readAsDataURL(file);
                }
			},
```			
### 压缩图片
上面做完图片数据的获取后，就可以做compress压缩图片的方法了。而压缩图片也并不是直接把图片绘制到canvas再调用一下toDataURL就行的。

　　在IOS中，canvas绘制图片是有两个限制的：

　　首先是图片的大小，如果图片的大小超过两百万像素，图片也是无法绘制到canvas上的，调用drawImage的时候不会报错，但是你用toDataURL获取图片数据的时候获取到的是空的图片数据。

　　再者就是canvas的大小有限制，如果canvas的大小大于大概五百万像素（即宽高乘积）的时候，不仅图片画不出来，其他什么东西也都是画不出来的。

　　应对第一种限制，处理办法就是瓦片绘制了。瓦片绘制，也就是将图片分割成多块绘制到canvas上，我代码里的做法是把图片分割成100万像素一块的大小，再绘制到canvas上。

　　而应对第二种限制，我的处理办法是对图片的宽高进行适当压缩，我代码里为了保险起见，设的上限是四百万像素，如果图片大于四百万像素就压缩到小于四百万像素。四百万像素的图片应该够了，算起来宽高都有2000X2000了。

　　如此一来就解决了IOS上的两种限制了。

　　除了上面所述的限制，还有两个坑，一个就是canvas的toDataURL是只能压缩jpg的，当用户上传的图片是png的话，就需要转成jpg，也就是统一用canvas.toDataURL('image/jpeg', 0.1) ， 类型统一设成jpeg，而压缩比就自己控制了。

　　另一个就是如果是png转jpg，绘制到canvas上的时候，canvas存在透明区域的话，当转成jpg的时候透明区域会变成黑色，因为canvas的透明像素默认为rgba(0,0,0,0)，所以转成jpg就变成rgba(0,0,0,1)了，也就是透明背景会变成了黑色。解决办法就是绘制之前在canvas上铺一层白色的底色。
　

    compress:function(img)  {
    var canvas = document.createElement("canvas");
    var ctx = canvas.getContext('2d');
        //    瓦片canvas
    var tCanvas = document.createElement("canvas");
     var tctx = tCanvas.getContext("2d");
        var initSize = img.src.length;
        var width = img.width;
        var height = img.height;

        //如果图片大于四百万像素，计算压缩比并将大小压至400万以下
        var ratio;
        if ((ratio = width * height / 4000000)>1) {
            ratio = Math.sqrt(ratio);
            width /= ratio;
            height /= ratio;
        }else {
            ratio = 1;
        }

        canvas.width = width;
        canvas.height = height;

    //        铺底色
        ctx.fillStyle = "#fff";
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        //如果图片像素大于100万则使用瓦片绘制
        var count;
        if ((count = width * height / 1000000) > 1) {
            count = ~~(Math.sqrt(count)+1); //计算要分成多少块瓦片

    //            计算每块瓦片的宽和高
            var nw = ~~(width / count);
            var nh = ~~(height / count);

            tCanvas.width = nw;
            tCanvas.height = nh;

            for (var i = 0; i < count; i++) {
                for (var j = 0; j < count; j++) {
                    tctx.drawImage(img, i * nw * ratio, j * nh * ratio, nw * ratio, nh * ratio, 0, 0, nw, nh);

                    ctx.drawImage(tCanvas, i * nw, j * nh, nw, nh);
                }
            }
        } else {
            ctx.drawImage(img, 0, 0, width, height);
        }

        //进行最小压缩
        var ndata = canvas.toDataURL('image/jpeg', 0.1);

        console.log('压缩前：' + initSize);
        console.log('压缩后：' + ndata.length);
        console.log('压缩率：' + ~~(100 * (initSize - ndata.length) / initSize) + "%");

        tCanvas.width = tCanvas.height = canvas.width = canvas.height = 0;

        return ndata;
    }
    
        

### 图片上传
完成图片压缩后，就可以塞进formdata里进行上传了，先将base64数据转成字符串，再实例化一个ArrayBuffer，然后将字符串以8位整型的格式传入ArrayBuffer，再通过BlobBuilder或者Blob对象，将8位整型的ArrayBuffer转成二进制对象blob，然后把blob对象append到formdata里，再通过ajax发送给后台即可。

```
getBlob:function(buffer, format) {
    try {
      return new Blob(buffer, {type: format});
    } catch (e) {
      var bb = new (window.BlobBuilder || window.WebKitBlobBuilder || window.MSBlobBuilder);
      buffer.forEach(function(buf) {
        bb.append(buf);
      });
      return bb.getBlob(format);
    }
  },
			// 上传图片的ajax请求
			async Upload2(fileData,type,index){
				console.log("执行了上传的第二个函数");
				var text = window.atob(fileData.split(",")[1]);
    var buffer = new Uint8Array(text.length);
    var pecent = 0, loop = null;
    for (var i = 0; i < text.length; i++) {
      buffer[i] = text.charCodeAt(i);
    }
    var blob = this.getBlob([buffer], type);
                let that = this;
				let form = new FormData();
				form.append("Filedata",blob);
				await myAjax.post( apiPath.upload ,form,{
					'headers':{
						'mimeType':'multipart/form-data'
					}
				} ).then( res => {
					
				} ).catch( e => {
				} );
               
				
			},
			// 上传图片的ajax请求
			async Upload(fileData,index){
                let that = this;
				let form = new FormData();
				form.append("Filedata",fileData);
				await myAjax.post( apiPath.upload ,form,{
					'headers':{
						'mimeType':'multipart/form-data'
					}
				} ).then( res => {
					
				} ).catch( e => {
				} );
               
			},
```

本文代码提供了大概的思路以及主要的代码，具体的demo自己动手做起来吧。


