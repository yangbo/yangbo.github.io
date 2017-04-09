---
layout: post
title:  "spring boot 开发入门示例"
date:   2017-04-09 13:36:00
categories: spring-boot spring-cloud micro-service
---

# 准备开发环境

## 使用 sdk man 安装 gradle

	curl -s "https://get.sdkman.io" | bash
	sdk install gradle
	
安装结束后，gradle 的安装路径在
	
	maven { url 'http://maven.aliyun.com/nexus/content/groups/public/'}
	
## 使用阿里的 maven 仓库

	maven { url 'http://maven.aliyun.com/nexus/content/groups/public/'}
	
## 用 start.spring.io 创建一个初始项目

访问 http://start.spring.io 按照提示创建初始项目模板，下载后解压开。

参考[Building Application with spring boot](https://spring.io/guides/gs/spring-boot/)
来创建一个 hello world web程序。

## 记得配置 spring-boot-start-web 依赖

更多的spring boot 文档可以参考下面：

[spring boot](http://docs.spring.io/spring-boot/docs/1.5.2.RELEASE/reference/htmlsingle)

## 尝试使用 spring-boot-devtools 模块

为了方便开发，可以添加一个模块 

	compile('org.springframework.boot:spring-boot-devtools')

这样修改程序后 spring boot 就会自动重启web 容器，达到快速预览的目的。

## 做单元测试

先要引入依赖模块

	testCompile('org.springframework.boot:spring-boot-starter-test')

然后编写单元测试类

	@RunWith(SpringRunner.class)
	@SpringBootTest
	@AutoConfigureMockMvc
	public class HelloControllerTest {
		@Autowired MockMvc mockMvc;
		
		@Test
		public void test() throws Exception{
			// 1. 构建一个 mock mvc request build
			MockHttpServletRequestBuilder request = MockMvcRequestBuilders.get("/").accept(MediaType.APPLICATION_JSON_UTF8);
			// 2. 用 mock mvc 执行请求
			mockMvc.perform(request).andExpect(status().isOk())
			.andExpect(content().string(equalTo("你好，欢迎使用 Spring Boot Web!")));
		}
	}
	
## 做集成测试

导入的包是

	import static org.hamcrest.CoreMatchers.equalTo;
	import static org.junit.Assert.assertThat;

编写测试类

	@RunWith(SpringRunner.class)
	@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)
	public class HelloControllerIT {
		
		@LocalServerPort
		private int port;
		
		private URI base;
		
		@Autowired
		private TestRestTemplate template;
		
		@Before
		public void before() throws Exception{
			this.base = new URI("http://localhost:" + this.port);
		}
		
		@Test
		public void testHello() {
			ResponseEntity<String> response = template.getForEntity(this.base, String.class);
			assertThat(response.getBody(), equalTo("你好，欢迎使用 Spring Boot Web!"));
		}
	}

# 开发一个 spring cloud 服务

## 用 start.spring.io 生成初始项目

选择 web, jpa, h2, devtool 模块

记得将生成的项目 build.gradle 中的 maven 首选仓库改为 阿里云 maven 仓库。

	maven { url 'http://maven.aliyun.com/nexus/content/groups/public/'}

jpa 的文档参考这里：

https://spring.io/guides/gs/accessing-data-jpa/
