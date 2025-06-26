---
layout: post
title: 百度通用文字识别 image format error （216201）错误解决
date: '2019-12-03 11:38:00 +0800'
categories: [小程序]
tags: [小程序, 文字识别, 百度OCR]
author: tusiman
---
根据文档说明使用base64的图片发送，结果出现错误

```
 {
 
    "error_code" = 216201;
 
    "error_msg" = "image format error";
 
    "log_id" = 9064900028040021949;
 
}
```
网上查了好多都没有解决，这里整理一下。

其实原因很简单，相信很多童鞋和我一样都加上了‘data:image/png;base64,’ ，其实是不用的，也不需要urlencode，感觉整个人都不好了，差点就放弃了。。。

有博友说还不行，直接上代码吧~~

```javascript
wx.chooseImage({
  success: res => {
	wx.getFileSystemManager().readFile({
	  filePath: res.tempFilePaths[0], //选择图片返回的相对路径
	  encoding: 'base64', //编码格式
	  success: res => { //成功的回调
		// 图片
		var image = res.data;
		// 文字识别
		wx.request({
		  url: 'https://aip.baidubce.com/rest/2.0/ocr/v1/general_basic?access_token=' + access_token,
		  data: {
			image: image
		  },
		  header: {
			'Content-Type': 'application/x-www-form-urlencoded'
		  },
		  method: 'POST',
		  success(res) {
			console.log(res.data)
		  }
		})
	  }
	})
  }
})
```
