---
title: servlet生成验证码
date: 2019-04-08 21:07:45
categories: Java Web
tags: 
- Servlet
- 验证码
---
今天正式学习了自动生成验证码的方法，其中用到了从没有接触过的Color类、Graphics类等，做一下纪录。

## 一、创建一个DrawImage Servlet，在服务器端生成验证码

```java
package lwq.response.study;

import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.util.Random;

import javax.imageio.ImageIO;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class DrawImage
 */
@WebServlet("/DrawImage")
public class DrawImage extends HttpServlet {
	private static final long serialVersionUID = 1L;

	private static final int WIDTH = 120;
	private static final int HEIGHT = 30;

	/**
	 * @see HttpServlet#HttpServlet()
	 */
	public DrawImage() {
		super();
		// TODO Auto-generated constructor stub
	}

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse
	 *      response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		this.doPost(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse
	 *      response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		String createTypeFlag = request.getParameter("createTypeFlag");// 接受客户端传递的createTypeFlag标识
		// 在内存中创建一张图片
		BufferedImage bi = new BufferedImage(WIDTH, HEIGHT, BufferedImage.TYPE_INT_BGR);
		// 得到图片
		Graphics gra = bi.getGraphics();
		// 设置图片的背景色
		setBackGround(gra);
		// 画干扰线
		setRandomline(gra);
		// 设置边框 
		setBorder(gra);
		// 写随机数
		//		String random = drawRandomNum((Graphics2D) gra, "ch");
		//		String random = drawRandomNum((Graphics2D)gra, "n");
		//		String random = drawRandomNum((Graphics2D)gra, "nl");
		//		String random = drawRandomNum((Graphics2D)gra, "l");
		String random = drawRandomNum((Graphics2D) gra, createTypeFlag);//根据客户端传递的createTypeFlag标识生成验证码图片
		// 将随机数存在session中 
		request.getSession().setAttribute("checkcode", random);
		response.setContentType("image/jpeg");// 等同于response.setHeader("Content-Type", "image/jpeg");
		// 设置响应头控制浏览器不要缓存
		response.setDateHeader("expries", -1);
		response.setHeader("Cache-Control", "no-cache");
		response.setHeader("Pragma", "no-cache");
		// 将图片写给浏览器
		ImageIO.write(bi, "jpg", response.getOutputStream());
	}

	/* 设置背景颜色 */
	private void setBackGround(Graphics gra) {
		gra.setColor(Color.BLACK);
		gra.fillRect(0, 0, WIDTH, HEIGHT);
	}

	/*
	 * 设置边框
	 */
	private void setBorder(Graphics gra) {
		gra.setColor(Color.BLUE);
		gra.fillRect(1, 1, WIDTH, HEIGHT);
	}

	/*
	 * 设置随机干扰线
	 */
	private void setRandomline(Graphics gra) {
		gra.setColor(Color.GREEN);
		for (int i = 0; i < 5; i++) {
			int x1 = new Random().nextInt(WIDTH);
			int y1 = new Random().nextInt(HEIGHT);
			int x2 = new Random().nextInt(WIDTH);
			int y2 = new Random().nextInt(HEIGHT);
			gra.drawLine(x1, y1, x2, y2);
		}
	}

	/* 生成随机字符 */

	private String drawRandomNum(Graphics2D gra, String... createTypeFlag) {
		gra.setColor(Color.RED);
		gra.setFont(new Font("宋体", Font.BOLD, 20));
		String baseNumLetter = "0123456789ABCDEFGHJKLMNOPQRSTUVWXYZ";
		String baseChineseChar = "\u7684\u4e00\u4e86\u662f\u6211\u4e0d\u5728\u4eba\u4eec\u6709\u6765\u4ed6\u8fd9\u4e0a"
				+ "\u7740\u4e2a\u5730\u5230\u5927\u91cc\u8bf4\u5c31\u53bb\u5b50\u5f97\u4e5f\u548c\u90a3\u8981\u4e0b\u770b"
				+ "\u5929\u65f6\u8fc7\u51fa\u5c0f\u4e48\u8d77\u4f60\u90fd\u628a\u597d\u8fd8\u591a\u6ca1\u4e3a\u53c8\u53ef"
				+ "\u5bb6\u5b66\u53ea\u4ee5\u4e3b\u4f1a\u6837\u5e74\u60f3\u751f\u540c\u8001\u4e2d\u5341\u4ece\u81ea\u9762"
				+ "\u524d\u5934\u9053\u5b83\u540e\u7136\u8d70\u5f88\u50cf\u89c1\u4e24\u7528\u5979\u56fd\u52a8\u8fdb\u6210"
				+ "\u56de\u4ec0\u8fb9\u4f5c\u5bf9\u5f00\u800c\u5df1\u4e9b\u73b0\u5c71\u6c11\u5019\u7ecf\u53d1\u5de5\u5411"
				+ "\u4e8b\u547d\u7ed9\u957f\u6c34\u51e0\u4e49\u4e09\u58f0\u4e8e\u9ad8\u624b\u77e5\u7406\u773c\u5fd7\u70b9"
				+ "\u5fc3\u6218\u4e8c\u95ee\u4f46\u8eab\u65b9\u5b9e\u5403\u505a\u53eb\u5f53\u4f4f\u542c\u9769\u6253\u5462"
				+ "\u771f\u5168\u624d\u56db\u5df2\u6240\u654c\u4e4b\u6700\u5149\u4ea7\u60c5\u8def\u5206\u603b\u6761\u767d"
				+ "\u8bdd\u4e1c\u5e2d\u6b21\u4eb2\u5982\u88ab\u82b1\u53e3\u653e\u513f\u5e38\u6c14\u4e94\u7b2c\u4f7f\u5199"
				+ "\u519b\u5427\u6587\u8fd0\u518d\u679c\u600e\u5b9a\u8bb8\u5feb\u660e\u884c\u56e0\u522b\u98de\u5916\u6811"
				+ "\u7269\u6d3b\u90e8\u95e8\u65e0\u5f80\u8239\u671b\u65b0\u5e26\u961f\u5148\u529b\u5b8c\u5374\u7ad9\u4ee3"
				+ "\u5458\u673a\u66f4\u4e5d\u60a8\u6bcf\u98ce\u7ea7\u8ddf\u7b11\u554a\u5b69\u4e07\u5c11\u76f4\u610f\u591c"
				+ "\u6bd4\u9636\u8fde\u8f66\u91cd\u4fbf\u6597\u9a6c\u54ea\u5316\u592a\u6307\u53d8\u793e\u4f3c\u58eb\u8005"
				+ "\u5e72\u77f3\u6ee1\u65e5\u51b3\u767e\u539f\u62ff\u7fa4\u7a76\u5404\u516d\u672c\u601d\u89e3\u7acb\u6cb3"
				+ "\u6751\u516b\u96be\u65e9\u8bba\u5417\u6839\u5171\u8ba9\u76f8\u7814\u4eca\u5176\u4e66\u5750\u63a5\u5e94"
				+ "\u5173\u4fe1\u89c9\u6b65\u53cd\u5904\u8bb0\u5c06\u5343\u627e\u4e89\u9886\u6216\u5e08\u7ed3\u5757\u8dd1"
				+ "\u8c01\u8349\u8d8a\u5b57\u52a0\u811a\u7d27\u7231\u7b49\u4e60\u9635\u6015\u6708\u9752\u534a\u706b\u6cd5"
				+ "\u9898\u5efa\u8d76\u4f4d\u5531\u6d77\u4e03\u5973\u4efb\u4ef6\u611f\u51c6\u5f20\u56e2\u5c4b\u79bb\u8272"
				+ "\u8138\u7247\u79d1\u5012\u775b\u5229\u4e16\u521a\u4e14\u7531\u9001\u5207\u661f\u5bfc\u665a\u8868\u591f"
				+ "\u6574\u8ba4\u54cd\u96ea\u6d41\u672a\u573a\u8be5\u5e76\u5e95\u6df1\u523b\u5e73\u4f1f\u5fd9\u63d0\u786e"
				+ "\u8fd1\u4eae\u8f7b\u8bb2\u519c\u53e4\u9ed1\u544a\u754c\u62c9\u540d\u5440\u571f\u6e05\u9633\u7167\u529e"
				+ "\u53f2\u6539\u5386\u8f6c\u753b\u9020\u5634\u6b64\u6cbb\u5317\u5fc5\u670d\u96e8\u7a7f\u5185\u8bc6\u9a8c"
				+ "\u4f20\u4e1a\u83dc\u722c\u7761\u5174\u5f62\u91cf\u54b1\u89c2\u82e6\u4f53\u4f17\u901a\u51b2\u5408\u7834"
				+ "\u53cb\u5ea6\u672f\u996d\u516c\u65c1\u623f\u6781\u5357\u67aa\u8bfb\u6c99\u5c81\u7ebf\u91ce\u575a\u7a7a"
				+ "\u6536\u7b97\u81f3\u653f\u57ce\u52b3\u843d\u94b1\u7279\u56f4\u5f1f\u80dc\u6559\u70ed\u5c55\u5305\u6b4c"
				+ "\u7c7b\u6e10\u5f3a\u6570\u4e61\u547c\u6027\u97f3\u7b54\u54e5\u9645\u65e7\u795e\u5ea7\u7ae0\u5e2e\u5566"
				+ "\u53d7\u7cfb\u4ee4\u8df3\u975e\u4f55\u725b\u53d6\u5165\u5cb8\u6562\u6389\u5ffd\u79cd\u88c5\u9876\u6025"
				+ "\u6797\u505c\u606f\u53e5\u533a\u8863\u822c\u62a5\u53f6\u538b\u6162\u53d4\u80cc\u7ec6";
		String baseNumber = "1234567890";
		String baseLetter = "QWERTYUIOPASDGFHJKLZXCVBNM";
		String str = createTypeFlag[0];
		if (createTypeFlag.length > 0 && str != null) {
			if(str.equals("ch")) {
				return createRandomChar(gra, baseChineseChar);
			}else if(str.equals("n")) {
				return createRandomChar(gra, baseNumber);
			}else if(str.equals("l")) {
				return createRandomChar(gra, baseLetter);
			}else if(str.equals("nl")){
				return createRandomChar(gra, baseNumLetter);
			}
		}else {
			//默认截取字母和数字的组合
			return createRandomChar(gra, baseNumLetter);
		}
		return "";
	}

	/* 创建随机字符 */
	private String createRandomChar(Graphics2D gra, String baseChar) {
		StringBuffer sb = new StringBuffer();
		int x = 5;
		String ch = "";
		for (int i = 0; i < 4; i++) {
			int degree = new Random().nextInt() % 30;
			ch = baseChar.charAt(new Random().nextInt(baseChar.length())) + "";
			sb.append(ch);

			gra.rotate(degree * Math.PI / 180, x, 20);
			gra.drawString(ch, x, 20);
			gra.rotate(-degree * Math.PI / 180, x, 20);
			x += 30;

		}
		return sb.toString();
	}
}

```
## 二、创建 index.jsp 在客户端浏览器中利用Form表单使用验证码图片

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<script type="text/javascript">
	//刷新验证码
	function changeImage() {
		document.getElementById("verificationCodeImg").src = "${pageContext.request.contextPath}/DrawImage?"
				+ Math.random();
	}
</script>
</head>
<body>
	<form action="${pageContext.request.contextPath}/CheckServlet"
		method="post">
		验证码：<input type="text" name="verificationCode"> <img
			alt="看不清，换一张"
			src="${pageContext.request.contextPath}/DrawImage"
			id="verificationCodeImg" onclick="changeImage()"> 
			<a href="javascript:void(0)" onclick="changeImage()">看不清，换一张</a> <br>
		<input type="submit" value="提交">
	</form>
</body>
</html>
```
## 三、创建CheckServlet服务器端对form表单提交上来的验证码处理

```java
package lwq.response.study;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class CheckServlet
 */
@WebServlet("/CheckServlet")
public class CheckServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public CheckServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String clientCheckcode = request.getParameter("verificationCode");//接受客户端浏览器传入的验证码
		String serverCheckcode = (String)request.getSession().getAttribute("checkcode");//从服务器端的session中取出验证码，这里的checkcode是在DrawImage类中存入的属性值
		if(clientCheckcode.equals(serverCheckcode)) {
			System.out.println("通过！");
		}else {
			System.out.println("失败！");
		}
		
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}

```

## 四、其他说明

在index.jsp中

```javascript
function changeImage() {
		document.getElementById("verificationCodeImg").src = "${pageContext.request.contextPath}/DrawImage?"
				+ Math.random();
	}
```

在末尾加Math.random()的作用：
如果两次请求地址一样，服务器只会处理第一次请求，第二次请求返回内容和第一次一样。或者说如果地址相同，第一次请求时，将自动缓存，导致第二次不会重复请求了。Math.random()是调用javascript语法中的数学函数，能够产生随机数。
末尾加Math.random()使每次请求地址不相同，服务器每次都去做不同的响应。也可以使用new date()时间戳的形式作为参数传递。



学习参考的博客是博客园大神“孤傲苍狼”的博客——https://www.cnblogs.com/xdp-gacl/p/3798190.html