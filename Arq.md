# 統合テスト ヲ
# 自動化セヨ


## 統合テストへの道

- ユニットテストは当然やっている
- DBとかアプリケーションサーバとかUIを含む再実行可能な統合テストは？
- ユニットテストだけでは「実際に組み合わせてない」不安感が拭えない
- やろうと思ったけどMock作るのがつらい
- Mock作ったけどその挙動が正しいかどうかわからない


## 手動統合テスト

- 膨大なExcelテスト仕様書
- 膨大なテスト実行コスト
- 即レガシー、触るな危険
- つらい


## 自動化された統合テスト

- 手動テストの最小化
- コスト削減
- レガシー脱却
- 楽しい



<!-- .slide: data-background="images/arq/arquillian_desktop_1024x800.jpg" -->



# Arquillian
<!-- .slide: data-background="images/arq/arquillian_icon_256px.png" data-background-size="256px" data-background-position="top right" -->

### 統合テストフレームワーク
### No more mocks. No more container lifecycle and deployment hassles. Just real tests!
http://arquillian.org/



## EJB
<!-- .slide: data-background="images/arq/arquillian_ui_warning_256px.png" data-background-size="256px" data-background-position="top right" -->

```java
@Stateless
public class HelloBean {
  public String sayHello(String name) {
    return "Hello " + name + "!";
  }
} 
```



## EJB Test
<!-- .slide: data-background="images/arq/arquillian_ui_success_256px.png" data-background-size="256px" data-background-position="top right" -->

```java
@RunWith(Arquillian.class)
public class HelloBeanTest {
  @Inject HelloBean hello;
  @Test
  public void shouldSayHello() throws Exception {
    Assert.assertEquals("Hello Earthling!",
                        hello.sayHello("Earthling"));
  }

  @Deployment
  public static WebArchive createDeployment() {
    return ...;
  }
} 
```



## Arquillianの機能
<!-- .slide: data-background="images/arq/arquillian_icon_256px.png" data-background-size="256px" data-background-position="top right" -->

- 本物の実行環境へデプロイ、本物の実行環境上でテスト
- テスト対象だけではなく、テストでも`@Inject`や`@PersistenceContext`などが利用可能
- IDE連携
- メジャーなアプリケーションサーバ全部対応
- 様々な拡張機能



<!-- .slide: data-background="images/arq/ja-guide.png" -->



## 事例
<!-- .slide: data-background="images/arq/arquillian_icon_256px.png" data-background-size="256px" data-background-position="top right" -->

- Google: Arquillian (日本語のみ検索)
  - https://www.google.co.jp/search?hl=ja&lr=lang_ja&q=arquillian
- Weld (JSR-299 CDI実装のコンテナ上でのテスト)
- DeltaSpike (CDIライブラリ)
  - http://deltaspike.apache.org/build.html
- 個人的にはJPAとかJava EEの機能を試したい時によく使います



# Arquillian Extensions
<!-- .slide: data-background="images/arq/arquillian_ui_crown_256px.png" data-background-size="256px" data-background-position="top right" -->



## Arquillian Persistence
<!-- .slide: data-background="images/arq/arquillian_icon_256px.png" data-background-size="256px" data-background-position="top right" -->

テスト毎にトランザクション、データ準備と照合

```java
@RunWith(Arquillian.class)
public class PersistenceTest { 
  @Deployment
  public static WebArchive deployment() {
    //... 
    .addAsManifestResource("test-persistence.xml","persistence.xml");
  }

  @Test
  @UsingDataSet("datasets/users.yml")
  @ShouldMatchDataSet("datasets/expected-users.yml")
  public void test() { //... } 
} 
```



## Arquillian Drone
<!-- .slide: data-background="images/arq/feature_browser.png" data-background-size="256px" data-background-position="top right" -->

Seleniumを使ったWebテスト、Ajaxもカバー

```java
@Drone WebDriver browser;
@ArquillianResource URL deploymentUrl;
@FindBy WebElement userName;
@FindBy WebElement password;
@FindBy(css = "input[type=submit]") WebElement whoAmI;

@Test
public void should_login_successfully() {
  browser.get(deploymentUrl.toExternalForm() + "login.jsf");
  userName.sendKeys("demo");
  password.sendKeys("demo");
  guardHttp(loginButton).click();
  assertEquals("Welcome", facesMessage.getText().trim());
  whoAmI.click();
  waitAjax().until().element(signedAs).is().present();
  assertTrue(signedAs.getText().contains("demo"));
}
```



## Arquillian Warp
<!-- .slide: data-background="images/arq/warp.jpg" data-background-size="256px" data-background-position="top right" -->

クライアントでテストをトリガー、サーバでassert

```java
@Drone WebDriver driver;

Warp
  .initiate(new Activity() { // executed on client side
    public void perform() {
      browser.navigate().to(contextPath + "index.jsf");
    }})
  .inspect(new Inspection() { // executed on server side
    @Inject IndexBean bean;
    @AfterPhase(RENDER_RESPONSE)
    public void verifyAccess() {
      Assert.assertTrue("true", bean.isAccessed());
    }
  });
```



## Other extensions
<!-- .slide: data-background="images/arq/arquillian_icon_256px.png" data-background-size="256px" data-background-position="top right" -->

- AngularJS
- Performance
- Spring
- Spock
- Guice
- QUnit
- Reporter
  - テストのスクリーンショット
  - デスクトップをビデオ撮影



# Mock is dead.
# Long live testing.
<!-- .slide: data-background="images/arq/arquillian_icon_256px.png" data-background-size="256px" data-background-position="top right" -->



<!-- .slide: data-background="images/arq/arquillian_desktop_1024x800.jpg" -->



## References / Links

- このスライド
  - http://nekop.github.io/slides/Arq.html
- このスライドの原稿
  - http://nekop.github.io/slides/Arq.md
- Java EE / ArquillianのMaven最小設定
  - https://github.com/nekop/java-examples/tree/master/maven



## References / Links

- Arquillianガイド
  - http://arquillian.org/guides/
- Alien Driven Development 
  - http://www.slideshare.net/myfear/alien-drivendevelopment
- Arquillian Presentations
  - http://aslakknutsen.github.io/presentations/presentations/
- Testing JSF applications with Arquillian and Selenium
  - http://www.bleathem.ca/talks/2012-JavaOne/testing-jsf.html
