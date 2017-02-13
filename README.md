# We-Canvas
##We-Canvas之WaveImage

###效果图： 

![](http://p1.bpimg.com/567571/0740556f855f2858.gif)

###实现细节： 

####1.js: 
---
<pre> drawImage:function(data){
	var that = this
	var p10= data[0][0];   /* 三阶贝塞尔曲线起点坐标值*/
	var p11= data[0][1];   /* 三阶贝塞尔曲线第一个控制点坐标值*/
	var p12= data[0][2];   /* 三阶贝塞尔曲线第二个控制点坐标值*/
	var p13= data[0][3];   /* 三阶贝塞尔曲线终点坐标值*/
	
	var p20= data[1][0];
	var p21= data[1][1];
	var p22= data[1][2];
	var p23= data[1][3];
	
	var p30= data[2][0];
	var p31= data[2][1];
	var p32= data[2][2];
	var p33= data[2][3];
	
	var t = factor.t;
	
	/*计算多项式系数 （下同）*/    
	var cx1 = 3*(p11.x-p10.x);
	var bx1 = 3*(p12.x-p11.x)-cx1;
	var ax1 = p13.x-p10.x-cx1-bx1;
	
	var cy1 = 3*(p11.y-p10.y);
	var by1 = 3*(p12.y-p11.y)-cy1;
	var ay1 = p13.y-p10.y-cy1-by1;
	
	var xt1 = ax1*(t*t*t)+bx1*(t*t)+cx1*t+p10.x;
	var yt1 = ay1*(t*t*t)+by1*(t*t)+cy1*t+p10.y;
	
	var cx2 = 3*(p21.x-p20.x);
	var bx2 = 3*(p22.x-p21.x)-cx2;
	var ax2 = p23.x-p20.x-cx2-bx2;
	
	var cy2 = 3*(p21.y-p20.y);
	var by2 = 3*(p22.y-p21.y)-cy2;
	var ay2 = p23.y-p20.y-cy2-by2;
	
	var xt2 = ax2*(t*t*t)+bx2*(t*t)+cx2*t+p20.x;
	var yt2 = ay2*(t*t*t)+by2*(t*t)+cy2*t+p20.y;
	
	
	
	 var cx3 = 3*(p31.x-p30.x);
	var bx3 = 3*(p32.x-p31.x)-cx3;
	var ax3 = p33.x-p30.x-cx3-bx3;
	
	var cy3 = 3*(p31.y-p30.y);
	var by3 = 3*(p32.y-p31.y)-cy3;
	var ay3 = p33.y-p30.y-cy3-by3;
	
	/*计算xt yt的值 */
	var xt3 = ax3*(t*t*t)+bx3*(t*t)+cx3*t+p30.x;
	var yt3 = ay3*(t*t*t)+by3*(t*t)+cy3*t+p30.y;
	factor.t +=factor.speed;
	ctx.drawImage("../../images/heart1.png",xt1,yt1,30,30);
	ctx.drawImage("../../images/heart2.png",xt2,yt2,30,30);
	ctx.drawImage("../../images/heart3.png",xt3,yt3,30,30);
	ctx.draw();
	if(factor.t>1){
	    factor.t=0;
	    cancelAnimationFrame(timer);
	    that.startTimer();
	}else{
	    timer =requestAnimationFrame(function(){
	        that.drawImage([[{x:30,y:400},{x:70,y:300},{x:-50,y:150},{x:30,y:0}],[{x:30,y:400},{x:30,y:300},{x:80,y:150},{x:30,y:0}],[{x:30,y:400},{x:0,y:90},{x:80,y:100},{x:30,y:0}]])
	  })
	}}
</pre>

####2.原理：
---
  a.通过绘制三条不同的三阶贝塞尔曲线，选取三张图片让其沿着各自的贝塞尔曲线运动，运动轨迹如下图：  

 ![](http://i1.piimg.com/567571/f056562040342c21.png)  

  b.计算三阶贝塞尔曲线x(t),y(t)的数学表达式。  
  三阶贝塞尔曲线是通过四个点来形成一条曲线，两个控制点，一个起点一个终点。  
  利用多项式系数即可得到x(t),y(t)的数学表达式： 
  	 
	cx = 3 * ( x1 - x0 )
	bx = 3 * ( x2 - x1 ) - cx
	ax = x3 - x0 - cx - bx
	cy = 3 * ( y1 - y0 )	
	by = 3 * ( y2 - y1 ) - cy
	ay = y3 - y0 - cy - by
	
	x(t) = ax * t ^ 3 + bx * t ^ 2 + cx * t + x0
	y(t) = ay * t ^ 3 + by * t ^ 2 + cy * t + y0  
这里画了三条贝塞尔曲线，套用公式三次即可，这里没有采用循环，如果贝塞尔曲线条数比较多时，可采用循环调用 ctx.drawImage，其中factor.t为三阶贝塞尔曲线的参数，取值范围[0,1], 最后调用ctx.draw(),并且设置定时器即可实现图片沿着贝塞尔曲线运动。
 
####Tip：
这里采用的定时器是通过requestAnimationFrame（）函数实现的， 弃用setInterval的原因是实际测试中有卡帧现象并且动画显示有细微的不连续。