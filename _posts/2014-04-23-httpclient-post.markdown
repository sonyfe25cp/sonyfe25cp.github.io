---
layout: post
title: "利用HTTPClient登录论坛更新帖子起到置顶效果"
date: 2014-04-23 15:55:02
categories: java httpclient
---

> 在学校论坛的叫卖场发了个帖子对自己的小店进行推广，无奈叫卖场的帖子太多，卖家同学们似乎都不厌其烦的刷新自己的帖子来博取首页位置，咱作为学计算机的人，当然要让机器来干活！

### HTTPClient

这里会用到HTTPClient，HTTPMime，原理就是用httpclient来模拟用户在浏览器上进行登录，然后打开对应的帖子，对帖子内容进行编辑，然后再提交编辑请求给服务器，这样服务器就以为是人工对帖子进行了更新，于是，霸占住首页了。

学校的论坛有个bug：验证码是固定图片地址，也就是说只要验证了一次就可以无限使用。

下面的代码中，依然保留了如何获取验证码的部分。只是在实际中没有使用它，这也是测试了多次之后的结论。

目前论坛帖子的编码还在进一步的探测，下面代码暂时有编码问题，会尽快FIX。

###上代码

{% highlight java linenos %}
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.io.IOUtils;
import org.apache.http.Header;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.NameValuePair;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.BasicCookieStore;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * 登录Bitunion
 * @author Sonyfe25cp
 * @date Apr 23, 2014
 */
public class TestLogin {
	Logger logger = LoggerFactory.getLogger(TestLogin.class);

	public static void main(String[] args) {
		TestLogin tl = new TestLogin();
		tl.run();
	}

	CloseableHttpClient httpclient;

	void run() {
		httpclient = HttpClients.custom()
				.setDefaultCookieStore(new BasicCookieStore()).build();//可以帮助记录cookie
		try {

			HttpPost loginPost = new HttpPost(
					"http://www.bitunion.org/logging.php?action=login");
			List<NameValuePair> nvps = new ArrayList<>();
			nvps.add(new BasicNameValuePair("action", "login"));
			nvps.add(new BasicNameValuePair("username", "mikki"));
			nvps.add(new BasicNameValuePair("password", "7600"));
			nvps.add(new BasicNameValuePair("cookietime", "3600"));
			nvps.add(new BasicNameValuePair("referer", "/home.php?"));
			nvps.add(new BasicNameValuePair("verify", "40422"));
			nvps.add(new BasicNameValuePair("verifyimgid",
					"20bbf91c2113e54306bb73e39642a5a0"));
			nvps.add(new BasicNameValuePair("styleid", "7"));
			nvps.add(new BasicNameValuePair("loginsubmit", "会员登录"));
			loginPost.setEntity(new UrlEncodedFormEntity(nvps, "GBK"));

			for (NameValuePair bnvp : nvps) {
				logger.info("nvp : {} -- {}", bnvp.getName(), bnvp.getValue());
			}
			logger.info("loginPost: {}", loginPost.toString());
			CloseableHttpResponse response2 = httpclient.execute(loginPost);
			logger.info("status: {}", response2.getStatusLine().getStatusCode());
			if (response2.getStatusLine().getStatusCode() == 200) {
				String html = IOUtils.toString(response2.getEntity()
						.getContent(), "GBK");
				// logger.info(html);
			}
			response2.close();

			showGet("http://www.bitunion.org/post.php?action=edit&fid=114&tid=10534806&pid=13965621&no=%C2%A5%D6%F7&auid=84956&page=1");

			updatePost();

			httpclient.close();
		} catch (ClientProtocolException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 获取验证码
	 */
	void getVerify() {
		try {
			HttpGet get = new HttpGet("http://www.bitunion.org");
			String userAgent = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/33.0.1750.152 Safari/537.36";
			get.setHeader("User-Agent", userAgent);
			CloseableHttpResponse response1;
			response1 = httpclient.execute(get);

			String verifyCodePath = "";
			String verifyCode = "";
			if (response1.getStatusLine().getStatusCode() == 200) {
				String html = IOUtils.toString(response1.getEntity()
						.getContent(), "GBK");
				// logger.info(html);
				Document doc = Jsoup.parse(html);
				Elements imgs = doc.select("img");
				for (Element img : imgs) {
					String url = img.attr("src");
					if (url.contains("verifyimg")) {
						verifyCodePath = url.substring(url.indexOf("=") + 1);
						logger.info(verifyCodePath);
						logger.info("请输入验证码：http://www.bitunion.org/{}", url);
						byte[] b = new byte[1024];
						int n = System.in.read(b);
						verifyCode = new String(b, 0, n);
						logger.info("验证码是: {}", verifyCode);
					}
				}
			}
			Header[] h = response1.getAllHeaders();
			for (Header hh : h) {
				logger.info("{} -- {}", hh.getName(), hh.getValue());
				get.setHeader(hh.getName(), hh.getValue());
			}
			response1.close();
		} catch (ClientProtocolException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	/**
	 * 更新帖子内容
	 */
	void updatePost() {

		String content = "三种下单方式： [size=5]1. 网站：[url=http://noprinter.cn]http://noprinter.cn[/url][/size] [size=5]2.直接加QQ:2536151131下单也可以的[/size] [size=5]3.直接来实体店跟店员MM下单也是非常欢迎的！[/size] 实体店：[box=#FF6666]菜市场二楼C6，从职消超市上二楼左手第二家就是～[/box] [size=5] 店内联系QQ：2536151131 电话：6894 8513 [/size] [size=6]教材复印单面4分啊，4分啊！！！[/size] 早8：00 --- 晚8：00 都开门的哦~~~~~ 新进胶装机和切纸机各一台，电子书胶装神马的最喜欢了～ :roll::laugh:";
		int r = (int) (Math.random() * 5);
		MultipartEntityBuilder meb = MultipartEntityBuilder.create();
		meb.addTextBody("editsubmit", "submit");
		meb.addTextBody("page", "1");
		meb.addTextBody("viewperm", "0");
		meb.addTextBody("tag", "527");
		meb.addTextBody("subject", "【NoPrinter打印店】论文打印我们更专业！！！",
				ContentType.APPLICATION_OCTET_STREAM);
		meb.addTextBody("origsubject", "【NoPrinter打印店】论文打印我们更专业！！！",
				ContentType.APPLICATION_OCTET_STREAM);
		meb.addTextBody("posticon", "0");
		meb.addTextBody("mode", "2");
		meb.addTextBody("font", "Verdana");
		meb.addTextBody("size", "3");
		meb.addTextBody("color", "White");
		meb.addTextBody("message", content + " " + r,
				ContentType.APPLICATION_OCTET_STREAM);
		meb.addTextBody("usesig", "1");
		meb.addTextBody("attachedit", "new");
		meb.addTextBody("fid", "114");
		meb.addTextBody("tid", "10563683");
		meb.addTextBody("pid", "14271442");
		meb.addTextBody("pid", "14271442");
		meb.addTextBody("postsubject", "【NoPrinter打印店】论文打印我们更专业！！！",
				ContentType.APPLICATION_OCTET_STREAM);

		HttpEntity he = meb.build();

		HttpPost post = new HttpPost(
				"http://www.bitunion.org/post.php?action=edit");
		post.setEntity(he);
		try {
			HttpResponse res = httpclient.execute(post);
			int code = res.getStatusLine().getStatusCode();
			logger.info("code: {}", code);
		} catch (ClientProtocolException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 测试显示随便一个登录后才能看的页面
	 * @param url
	 */
	void showGet(String url) {
		HttpGet get = new HttpGet(url);
		try {
			HttpResponse response = httpclient.execute(get);
			int code = response.getStatusLine().getStatusCode();
			switch (code) {
			case 200:
				String html = IOUtils.toString(response.getEntity()
						.getContent(), "GBK");
				logger.info(html);
				break;
			default:
				logger.info("code: {}", code);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

}

{% endhighlight%}
