关于获取用户上传图片，并处理成base64上传到服务器的方法，在这里采用了exif的技术，可以直接down整个test项目查看



### 1、通过触发selectFileImage()方法				
	<input type="file"  name="pic"  id="uploadfile" accept="image/*" capture="camera" onchange="selectFileImage(this)"/>

### 2、待裁切页面展示，调用cutShow(tu)；	
### 3、在指定位置裁切整个图片，调用cutcut()；

#### 总结，主要难点，在于获取图片在裁切区域的x，y轴数据，以及canvas的画图

#### 自己写了几个方法

#### 获取两根手指之间的距离，用来计算缩放比例
	function getS(ev){
		var x1=ev.targetTouches[0].pageX;
		var x2=ev.targetTouches[1].pageX;
		var y1=ev.targetTouches[0].pageY;
		var y2=ev.targetTouches[1].pageY;
		var a=x1-x2;
		var b=y1-y2;
		var c=Math.sqrt(a*a+b*b);
		return c;
	}
#### 重置参数，每次触控前，需要先清数据，再赋予数据
	function resetElement() {
		x=start_X;
		y=start_Y;
		edsX=0;
		edsY=0;
		s=1;
		c={
			x:start_X+imgW/2,
			y:start_Y+imgH/2,

		}
		getC();
	}
#### 单点触控，只改变box的位置

	function fnAct(){
		hit.style.WebkitTransform='translate3d('+x+'px,'+y+'px,0px) scale('+s+','+s+')';
		hit.style.transform='translate3d('+x+'px,'+y+'px,0px) scale('+s+','+s+')';
		console.log(x,y);
	}

#### 图片滑动到区域外，限制滑出的方法
	function fnLimit(){
		var disW = (imgW*s-imgW)/2;
		var disH = (imgH*s-imgH)/2;
		var beyondW = Math.abs(imgW*s-cutImgW);
		var beyongH = Math.abs(imgH*s-cutImgH);

		if(imgW*s<=cutImgW&&imgH*s<=cutImgH){
			minX=0+disW;
			minY=0+disH;
			maxX=cutImgW-imgW*s+disW;
			maxY=cutImgH-imgH*s+disH;
			changeXY();
		}else if(imgW*s<=cutImgW&&imgH*s>cutImgH){
			minX=0+disW;
			minY=0+disH-beyongH;
			maxX=cutImgW-imgW*s+disW;
			maxY=cutImgH-imgH*s+disH+beyongH;
			changeXY();
		}else if(imgW*s>cutImgW&&imgH*s<=cutImgH){
			minX=0+disW-beyondW;
			minY=0+disH;
			maxX=cutImgW-imgW*s+disW+beyondW;
			maxY=cutImgH-imgH*s+disH;
			changeXY();
		}else if(imgW*s>cutImgW&&imgH*s>cutImgH){
			minX=0+disW-beyondW;
			minY=0+disH-beyongH;
			maxX=cutImgW-imgW*s+disW+beyondW;
			maxY=cutImgH-imgH*s+disH+beyongH;
			changeXY();
		}
		function changeXY(){
			if(x<minX){
				x=minX;
			}
			if(x>maxX){
				x=maxX;
			}
			if(y<minY){
				y=minY;
			}
			if(y>maxY){
				y=maxY;
			}

		}
		fnAct();
	}

#### 关于canvas画图，需要解决跨域问题，和图片加载问题，图片必须先加载一遍，才能画图，loadpng()就是用了递归的思想
	
  	var changeY = 86;
	var oC=document.createElement('canvas');
	var gd=oC.getContext('2d');
	oC.width = 640;
	oC.height = 3146+changeY;
	var iNow = 0;
	var arr = [
		sourceUrl + '/images/com_down.jpg',
		sourceUrl + '/images/show.png',
		sourceUrl + '/images/com_back.jpg',
		testLogo.src
	];
	loadPng();
	function loadPng(){
		var oImg = new Image();
		oImg.src = arr[iNow];
		oImg.onload = function(){
			if(iNow<arr.length-1){
				iNow++;
				loadPng();
			}else{
				fnDraw();
			}
		};
	}

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  






