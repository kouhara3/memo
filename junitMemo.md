
引用するにはまずログインしてください
引用範囲
はじめに 半年前くら...url-pattern>
 コンピュータクワガタ
コンピュータクワガタ
かっぱのかっぱによるコンピュータ関連のサイトです
2013-04-02
Spring MVC 3.2のSpring MVC Testを触った
 Spring
Ads by Google
子どものADHD。正しい理解を
育て方・しつけが問題ではありません。
ADHDの正しい理解と対処方を知ろう！
adhd.co.jp
はじめに

半年前くらいからSpring MVCについて勉強していまして、テストをどうしようかと思っていたところ、先日Spring 3.2がリリースされました。Spring 3.2からはSpring MVC Testが正式にサポートされるようになり、Spring MVCのテストが簡単に書けるようになりました。

そこで、テストアプリケーションをテストしてわかったことを、簡単にまとめてみました。

ショーケースのサンプルを見ながらやっています。こうしたほうがいいよとか、ここ違うよとかあれば突っ込んでいただけたら幸いです。

以下のファイルは、https://github.com/kuwalab/spring-mvctestに置いてあります。Eclipse＋WTPで作っています。
Mavenを利用して作成していますので、依存しているライブラリーはpom.xmlを参照してください。

最初のサンプル

まず、簡単なサンプルを通して、Spring MVCのテストをどのように行えるのかを確認します。

最初にweb.xmlを確認します。web.xmlはこれ以降特に変更はしません。

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
id="serv" version="3.0">
 <display-name>test</display-name>
 <welcome-file-list>
  <welcome-file>index.jsp</welcome-file>
 </welcome-file-list>
 <listener>
  <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
 </listener>
 <context-param>
  <param-name>defaultHtmlEscape</param-name>
  <param-value>true</param-value>
 </context-param>
 <context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value></param-value>
 </context-param>
 <filter>
  <filter-name>CharacterEncodingFilter</filter-name>
  <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  <init-param>
   <param-name>encoding</param-name>
   <param-value>utf-8</param-value>
  </init-param>
  <init-param>
   <param-name>forceEncoding</param-name>
   <param-value>true</param-value>
  </init-param>
 </filter>
 <filter-mapping>
  <filter-name>CharacterEncodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
 </filter-mapping>
 <servlet>
  <servlet-name>dispatcher</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
   <param-name>contextConfigLocation</param-name>
   <param-value>/WEB-INF/spring/beans-webmvc.xml</param-value>
  </init-param>
  <multipart-config>
   <max-file-size>10485760</max-file-size><!-- 10 MB -->
   <max-request-size>104857600</max-request-size><!-- 100 MB -->
   <file-size-threshold>1048576</file-size-threshold>
  </multipart-config>
 </servlet>
 <servlet-mapping>
  <servlet-name>dispatcher</servlet-name>
  <url-pattern>/</url-pattern>
 </servlet-mapping>
 <jsp-config>
  <jsp-property-group>
   <url-pattern>*.jsp</url-pattern>
   <el-ignored>false</el-ignored>
   <page-encoding>utf-8</page-encoding>
   <scripting-invalid>true</scripting-invalid>
   <include-prelude>/WEB-INF/jsp/common/common.jsp</include-prelude>
  </jsp-property-group>
 </jsp-config>
</web-app>
重要なのはDispatcherServletが読み込む設定ファイルで、今回は/WEB-INF/spring/beans-webmvc.xmlを使っています。URLは/*としてすべてのURLに対して割り当てています。

あとは、jsp-configで共通のカスタムタグ等を読み込んでいます。この辺りは通常のServletと同様です。

DispatcherServiletの設定は、beans-webmvc.xmlで行なっています。

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:mvc="http://www.springframework.org/schema/mvc"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.1.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd">
 <bean id="messageSource"
 class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
  <property name="basename" value="classpath:/messages" />
 </bean>
 <bean id="validator"
 class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
  <property name="validationMessageSource" ref="messageSource" />
 </bean>
 <bean id="multipartResolver"
  class="org.springframework.web.multipart.support.StandardServletMultipartResolver" />
 <!-- Spring MVCアノテーション利用設定 -->
 <mvc:annotation-driven validator="validator" />
 <context:component-scan base-package="com.example.spring.mvctest">
 </context:component-scan>

 <!-- View Resolverの設定 -->
 <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  <property name="prefix" value="/WEB-INF/jsp/" />
  <property name="suffix" value=".jsp" />
 </bean>
</beans>
コンポーネントは、com.exapmle.spring.mvctest以下に置きます。その他は特段注意することはありません。

最初のテストのために、テスト対象の最小限のControllerを以下のように作成します。「/」へのGETリクエストに対してindexというview名を返しています。実際に動かして確認する場合には、/WEB-INF/jsp/index.jspを作成することでブラウザを介して動作するようになります。

package com.example.spring.mvctest;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class SampleController {
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String index() {
        return "index";
    }
}
このControllerに対してのテストを書くと以下のようになります。

package com.example.spring.mvctest;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.setup.MockMvcBuilders.*;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.web.context.WebApplicationContext;

@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
@ContextConfiguration(locations = { "file:src/main/webapp/WEB-INF/spring/beans-webmvc.xml" })
public class SampleControllerTest {
    @Autowired
    private WebApplicationContext wac;

    private MockMvc mockMvc;

    @Before
    public void setup() {
        mockMvc = webAppContextSetup(wac).build();
    }

    @Test
    public void slashへのGET() throws Exception {
        mockMvc.perform(get("/")).andExpect(status().isOk())
                .andExpect(view().name("index"))
                .andExpect(model().hasNoErrors());
    }

    @Test
    public void slashへのPOST_許可されていないメソッド() throws Exception {
        mockMvc.perform(post("/")).andExpect(status().isMethodNotAllowed());
    }
}
Spring MVCのテストのためには、JUnitのClassRunnerにSprngJUnit4ClassRunnerを指定して、さらに@WebAppConfigurationを指定します。

Springの設定ファイルは、@ContextConfigurationのlocations属性で指定します。指定は文字列の配列でSpring一般の書式が使えます。上記の例だと、fileの相対パスを指定しています。他の指定方法等は、ドキュメントを参照してください。

ここまでで、WebApplicationContextを@Autowiredでインジェクションできるようになります。このWetApplicationContextは@ContextConfigurationで指定したファイルで設定されたものになります。

setupメソッドは@Beroreアノテーションをつけているため各テストケースのメソッドが呼び出される前に呼び出されます。これは、JUnit4の仕組みです。
setupメソッドではSpring MVCのテストをするためのMockMvcクラスをWebApplicationContextを使用して初期化しています。

具体的なテストは@Testがついたメソッドになります。最初に、「slashへのGET」メソッドを確認します。

@Test
public void slashへのGET() throws Exception {
    mockMvc.perform(get("/")).andExpect(status().isOk())
            .andExpect(view().name("index"))
            .andExpect(model().hasNoErrors());
}
MockMvcではperformメソッドでコントローラに対して処理を実行します。performメソッドは、MockMvc#performはRequestBuilderインタフェースの実装を引数に指定します。

RequestBuilderの実装クラスは、自分で作るのではなくMockMvcRequestBuildersのメソッドを用います。基本的には以下のHTTP Methodに対応したメソッドを使います。

メソッド	説明
delete(String urlTemplate, Object... urlVariables)	urlTemplateへのdeleteリクエスト
get(String urlTemplate, Object... urlVariables)	urlTemplateへのgetリクエスト
post(String urlTemplate, Object... urlVariables)	urlTemplateへのpostリクエスト
put(String urlTemplate, Object... urlVariables)	urlTemplateへのputリクエスト
ここでは、/へのgetリクエストを作成しています。このメソッドでリクエスト自体は投げられた格好になります。それ以降のメソッドがリクエスト実行後の処理のテストになります。まず、MockMvc#performの戻り値は、ResultActionsインタフェースになります。ResultActionでは、Controllerを実行した結果をandExpectメソッドを使いテストすることができます。

andExpectの引数はResultMatcherインタフェースになっていて、様々なテストをこなうことができます。

ResultMatcherのテストは、MockMvcResultMatchersの各メソッドを使うと簡単にできるようになっている。

最初にレスポンスで返されるHTTPステータスコードの確認用のstatusメソッドを確認します。statusメソッドは、StatusResultMatchersクラスのインスタンスを返します。リクエストに対して、予想されるメソッドを指定することでテストできます。最初の例では、ステータスコード200を返すので、isOk()メソッドでテストすることができます。is(int status)メソッドでステータスコードそのものを入れることもできます。

Viewの確認は、viewメソッドを使用します。viewメソッドは、ViewResultMatchersクラスのインスタンスを返します。リクエストに対して、期待されるViewのテストができます。上記の例の場合には、view().name(String viewName)メソッドで、View名の確認をしています。

Viewに対応するModelの確認は、今までと同じようにmodelメソッドを使用します。Modelは上記の例では特に何もしていないので、エラーが無いかどうかだけを確認しています。詳しくは後の例で確認します。

異常系のテスト

ControllerのテストですべてのHTTPメソッドを試す必要性はともかく、最初の例にPOSTリクエストを投げエラーコードが返ってくるかを確認するコードは以下になります。

@Test
public void slashへのPOST_許可されていないメソッド() throws Exception {
    mockMvc.perform(post("/")).andExpect(status().isMethodNotAllowed());
}
/へのリクエストはGETメソッドのみ受け付けているので、POSTはエラーになります。ここでは、isMethodNotAllowedでテストしています。

リクエストパラメータを渡す例

次はリクエストパラメータを処理し、結果を返すパターンで考えます。2つの数値を入れて加算した結果を返す画面を作成します。
入力チェックはあとにして、整数値が入力されることを期待したControllerを作成します。Controllerは初期画面表示のaddと入力された数値を加算し、結果を表示するcalcメソッドの2つからなります。

@RequestMapping(value = "/add", method = RequestMethod.GET)
public String add(Model model) {
    model.addAttribute("addModel", new AddModel());
    return "add";
}

@RequestMapping(value = "/calc", method = RequestMethod.POST)
public String calc(@ModelAttribute("addModel") AddModel addModel,
        Model model) {
    addModel.calc();
    model.addAttribute("addModel", addModel);

    return "calc";
}
add.jspは以下のように2つの入力フィールドを用意します。

<%@page contentType="text/html; charset=utf-8" %><%--
--%><!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8">
  <title>足し算のページ</title>
 </head>
 <body>
  <c:url value="/calc" var="calc" />
  <form:form modelAttribute="addModel" action="${calc}" method="POST">
   <form:input path="num1" size="3" /> + <form:input path="num2" size="3" /> <%--
--%><input type="submit" value="＝">
  </form:form>
 </body>
</html>
計算結果はcalc.jspで表示します。

<%@page contentType="text/html; charset=utf-8" %><%--
--%><!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8">
  <title>答えのページ</title>
 </head>
 <body>
  <c:out value="${addModel.num1}" /> + <%--
 --%><c:out value="${addModel.num2}" /> = <%--
 --%><c:out value="${addModel.answer}" />
 </body>
</html>
この例のテストケースを作成していきます。最初に/addへのリクエストのテストです。

@Test
public void slash_addへのGET() throws Exception {
    MvcResult mvcResult = mockMvc.perform(get("/add"))
            .andExpect(status().isOk())
            .andExpect(model().attributeExists("addModel")).andReturn();

    // Modelの中身までテストする場合には、equalsToメソッドを実装するかMvcResultからModelを取得しないといけない
    ModelMap modelMap = mvcResult.getModelAndView().getModelMap();
    Object object = modelMap.get("addModel");
    assertThat(object, is(not(nullValue())));
    assertThat(object, is(instanceOf(AddModel.class)));
    AddModel addModel = (AddModel) object;
    assertThat(addModel.getNum1(), is(nullValue()));
    assertThat(addModel.getNum2(), is(nullValue()));
    assertThat(addModel.getAnswer(), is(nullValue()));
}
単純に、画面を初期化して表示するだけですので例外のケースは特に考慮しません。

このパターンからModelのテストが入ります。Modelはmodelメソッドを使用してテストします。modelメソッドは、ModelResultMatchersクラスのインスタンスを返します。ModelResultMatchersクラスの代表的なメソッドは以下のとおりです。

メソッド	説明
attribute(String name, org.hamcrest.Matcher matcher)	nameで指定された属性をmatcherでテストします。
attributeExists(String... names)	namesで指定された属性がすべて存在するかどうかのテスト
attribute(String name, Object value)	nameで指定された属性の値がvalueオブジェクトと同じかどうかのテスト。内部的にはequalToでテストするため、equalsToメソッドを実装する必要があります。
hasErrors()	Validatorや任意のエラーがあることをテストします。
hasNoErrors()	Validatorや任意のエラーがないことをテストします。
errorCount(int expectedCount)	Validatorや任意のエラーの数をテストします。
attributeHasFieldErrors(String name, String... fieldNames)	name属性に格納されたModelのfiledNamsのフィールドにエラーが有ることをテストします。
上記の例では、addModelが属性として追加されていることを確認しています。また、Modelに格納されたオブジェクトの内容の確認についてはattributeメソッドでも確認できますが、MvcResult#andReturnメソッドを使いMvcResultのインスタンスからModelAndViewクラスのインスタンスを取得しても確認ができます。

次に、リクエストパラメータをテストに含めます。加算の結果を求めるため「=」ボタンを押した時の処理で確認します。

@Test
public void slash_calcへのPOST() throws Exception {
    MvcResult mvcResult = mockMvc
            .perform(post("/calc").param("num1", "1").param("num2", "2"))
            .andExpect(status().isOk())
            .andExpect(model().attributeExists("addModel")).andReturn();

    // Modelの中身までテストする場合には、MvcResultからModelを取得しないといけない
    ModelMap modelMap = mvcResult.getModelAndView().getModelMap();
    Object object = modelMap.get("addModel");
    assertThat(object, is(not(nullValue())));
    assertThat(object, is(instanceOf(AddModel.class)));
    AddModel addModel = (AddModel) object;
    assertThat(addModel.getNum1(), is(1));
    assertThat(addModel.getNum2(), is(2));
    assertThat(addModel.getAnswer(), is(3));
}
使用しているAddModelは以下のとおりです。

package com.example.spring.mvctest;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;

public class AddModel {
    @NotNull
    @Min(0)
    @Max(9_999)
    private Integer num1;
    @NotNull
    @Min(0)
    @Max(9_999)
    private Integer num2;
    private Integer answer;

    public void calc() {
        answer = num1 + num2;
    }

// getter、setterは省略
}
AddModelにはValidation用のアノテーションがついていますが、Validationをしていないのでここでは無視されます。

数値に、1と2をいれて計算しています。パラメータは、getやpostメソッドの戻り値のRequestBuilderのparamメソッドを使います。paramメソッドの1番目の引数がパラメータ名、2番目がその値になります。add.jsp画面ではinputフォームでnum1とnum2を定義しています。上記の例ではそれぞれに1と2の値を設定して、/calcにpostすることをエミュレートできます。入力され、加算された結果をModelから確認しています。

エラーメッセージを含む例の確認

前の例では入力チェックをしていないため、数値以外を入れるとスタックトレースが吐かれてしまいます。そこで、標準のValidatorを使って入力チェックをします。最初に先の例に入力チェックをつけた例を確認します。Controllerから見ていきます。

@RequestMapping(value = "/add2", method = RequestMethod.GET)
public String add2(Model model) {
    model.addAttribute("addModel", new AddModel());
    return "add2";
}

@RequestMapping(value = "/calc2", method = RequestMethod.POST)
public String calc2(@Valid @ModelAttribute("addModel") AddModel addModel,
        Errors errors, Model model) {
    if (errors.hasErrors()) {
        return "add2";
    }
    addModel.calc();
    model.addAttribute("addModel", addModel);

    return "calc2";
}
先の例と分けるため、add2、calc2メソッドとしています。add2は戻り値のView名以外は変更ありません。

calc2はModelAttributeに@Validアノテーションを付けて、Bean Validationを行うようにしています。@Validをつけた引数の次の引数にValidationのエラーが格納されるため、2番目の引数はErrorsにしています。

jspは、遷移先のURLが変更されているだけなので割愛します。

このControllerのテストを確認します。

@Test
public void slash_add2へのGET() throws Exception {
    MvcResult mvcResult = mockMvc.perform(get("/add2"))
            .andExpect(status().isOk())
            .andExpect(model().attributeExists("addModel")).andReturn();

    // Modelの中身までテストする場合には、MvcResultからModelを取得しないといけない
    ModelMap modelMap = mvcResult.getModelAndView().getModelMap();
    Object object = modelMap.get("addModel");
    assertThat(object, is(not(nullValue())));
    assertThat(object, is(instanceOf(AddModel.class)));
    AddModel addModel = (AddModel) object;
    assertThat(addModel.getNum1(), is(nullValue()));
    assertThat(addModel.getNum2(), is(nullValue()));
    assertThat(addModel.getAnswer(), is(nullValue()));
}

@Test
public void slash_calc2へのPOST() throws Exception {
    MvcResult mvcResult = mockMvc
            .perform(post("/calc2").param("num1", "1").param("num2", "2"))
            .andExpect(status().isOk())
            .andExpect(model().attributeExists("addModel")).andReturn();

    // Modelの中身までテストする場合には、MvcResultからModelを取得しないといけない
    ModelMap modelMap = mvcResult.getModelAndView().getModelMap();
    Object object = modelMap.get("addModel");
    assertThat(object, is(not(nullValue())));
    assertThat(object, is(instanceOf(AddModel.class)));
    AddModel addModel = (AddModel) object;
    assertThat(addModel.getNum1(), is(1));
    assertThat(addModel.getNum2(), is(2));
    assertThat(addModel.getAnswer(), is(3));
}

@Test
public void slash_calc2へのPOST_validatorのテスト() throws Exception {
    mockMvc.perform(post("/calc2").param("num1", "").param("num2", ""))
            .andExpect(status().isOk())
            .andExpect(view().name("add2"))
            .andExpect(model().attributeExists("addModel"))
            .andExpect(model().hasErrors())
            .andExpect(model().errorCount(2))
            .andExpect(
                    model().attributeHasFieldErrors("addModel", "num1",
                            "num2"));
    mockMvc.perform(post("/calc2").param("num1", "9999").param("num2", ""))
            .andExpect(status().isOk()).andExpect(view().name("add2"))
            .andExpect(model().attributeExists("addModel"))
            .andExpect(model().hasErrors())
            .andExpect(model().errorCount(1))
            .andExpect(model().attributeHasFieldErrors("addModel", "num2"));
    mockMvc.perform(post("/calc2").param("num1", "").param("num2", "9999"))
            .andExpect(status().isOk()).andExpect(view().name("add2"))
            .andExpect(model().attributeExists("addModel"))
            .andExpect(model().hasErrors())
            .andExpect(model().errorCount(1))
            .andExpect(model().attributeHasFieldErrors("addModel", "num1"));
    mockMvc.perform(
            post("/calc2").param("num1", "100000").param("num2", "100000"))
            .andExpect(view().name("add2"))
            .andExpect(status().isOk())
            .andExpect(model().attributeExists("addModel"))
            .andExpect(model().hasErrors())
            .andExpect(model().errorCount(2))
            .andExpect(
                    model().attributeHasFieldErrors("addModel", "num1",
                            "num2"));
}
add2のテストに関しては、addと同様です。

calc2ではValidatotionのテストをしています。正常系の処理はこれもcalcと同様です。ここでは、slash_calc2へのPOST_validatorのテストメソッドを確認します。

Validationのテストはまとめて記載しています。最初のテストは必須入力のチェックです。

mockMvc.perform(post("/calc2").param("num1", "").param("num2", ""))
        .andExpect(status().isOk())
        .andExpect(view().name("add2"))
        .andExpect(model().attributeExists("addModel"))
        .andExpect(model().hasErrors())
        .andExpect(model().errorCount(2))
        .andExpect(
                model().attributeHasFieldErrors("addModel", "num1",
                        "num2"));
Validationでエラーの場合には、エラーメッセージがModelに登録されます。まず、エラーがあるかどうかをhasErros()メソッドで確認できます。また、エラーの総数をerrorCountで確認でき、Modelのどのフィールドに対してエラーがあるのかをattributeHasFieldErrorsメソッドで確認できます。

以降も同様に、数値の最大値チェックや片方の1項目のみのエラー等を確認しています。

Validationで発生したエラーメッセージは、以下のルールでModelに格納されます。

"org.springframework.validation.BindingResult" + Bean Validationのモデルの名称
ここでは、AddModelが対象のため、

"org.springframework.validation.BindingResult.addModel"
Modelには、この属性名でBindingResultのインスタンスが格納されています。そのため、この属性は以下のように取り出し、確認することができます。

// エラーメッセージは、「org.springframework.validation.BindingResult.モデル名」に格納される。
Object object = mav.getModel().get(
        "org.springframework.validation.BindingResult.addModel");
assertThat(object, is(not(nullValue())));
assertThat(object, is(instanceOf(BindingResult.class)));
BindingResult bindingResult = (BindingResult) object;
BindingResultからはエラーメッセージのコードを取得することができます。BindingResultからフィールドごとのエラーは、getFieldErrorsメソッドで取り出せます。

// num1のエラーを取り出しテスト
List<FieldError> list = bindingResult.getFieldErrors("num1");
assertThat(list, is(not(nullValue())));
assertThat(list.size(), is(1));
getFieldErrorsはFieldErrorクラスのListを取得できます。そこから、FieldErrorを取得し、メッセージのコードを確認します。

FieldError fieldError = list.get(0);
assertThat(fieldError.getCode(), is("NotNull"));
Object[] args = fieldError.getArguments();
assertThat(args.length, is(1));
assertThat(args[0],
        is(instanceOf(DefaultMessageSourceResolvable.class)));
DefaultMessageSourceResolvable dmr = (DefaultMessageSourceResolvable) args[0];
assertThat(dmr.getCode(), is("num1"));
ファイルのダウンロード

適当なファイルのダウンロードを以下のように作成します。完全にWindows向けに改行コードをCR+LFとして、ファイルのエンコードはWindows-31Jとしています。

テストのためのテストなので、リテラルをダウンロードしています。

private static final MediaType MEDIA_TYPE_CSV = new MediaType(
        "application", "octet-stream", Charset.forName("Windows-31J"));
private static final String CRLF = "\r\n";

private String encodeFileName(String fileName) {
    try {
        return MimeUtility.encodeWord(fileName, "ISO-2022-JP", "B");
    } catch (UnsupportedEncodingException e) {
        // 来ないはず。
        return "csv.txt";
    }
}

@RequestMapping(value = "/csv", method = RequestMethod.GET)
public ResponseEntity<String> csv() {
    HttpHeaders httpHeaders = new HttpHeaders();
    httpHeaders.setContentDispositionFormData("attatchment",
            encodeFileName("顧客名簿.csv"));
    httpHeaders.setContentType(MEDIA_TYPE_CSV);

    StringBuilder sb = new StringBuilder();
    sb.append("名前,年齢").append(CRLF);
    sb.append("山田太郎,28").append(CRLF);
    sb.append("鈴木四郎,44").append(CRLF);
    sb.append("田中花子,19").append(CRLF);

    return new ResponseEntity<String>(sb.toString(), httpHeaders,
            HttpStatus.OK);
}
ファイルのダウンロードは以下のように行います。

@Test
public void slash_csv_へのGETのテスト() throws Exception {
    StringBuilder sb = new StringBuilder();
    sb.append("名前,年齢\r\n");
    sb.append("山田太郎,28\r\n");
    sb.append("鈴木四郎,44\r\n");
    sb.append("田中花子,19\r\n");

    mockMvc.perform(get("/csv"))
            .andExpect(
                    content().contentType(
                            "application/octet-stream;charset=windows-31j"))
            .andExpect(content().string(sb.toString()));
}
厳密に見ようと思えばもっと厳密に見ることができますが、ここは手を抜いています。

ファイルのアップロード

ファイルのアップロードのテストもフレームワークとして完全にサポートしているため簡単に行えます。

まず、ファイルのアップロードのControllerを作成します。

uploadFormはアップロード用のフォーム表示で、実際のアップロードはuploadメソッドで行なっています。

@RequestMapping(value = "/uploadForm", method = RequestMethod.GET)
public String uploadForm() {
    return "uploadForm";
}

@RequestMapping(value = "/upload", method = RequestMethod.POST, consumes = { "multipart/form-data" })
public String upload(@RequestParam("uploadFile") MultipartFile uploadFile,
        RedirectAttributes redirectAttributes) {
    try {
        File file = new File(System.getProperty("java.io.tmpdir"),
                uploadFile.getOriginalFilename());
        uploadFile.transferTo(file);
        redirectAttributes.addFlashAttribute("saveLocation",
                file.getCanonicalPath() + "に保管しました。");
    } catch (IOException e) {
        return "uploadForm";
    }

    return "redirect:/uploadForm";
}
jspは、uploadForm.jspのみで以下のようになります。普通のアップロード用のフォームです。

<%@page contentType="text/html; charset=utf-8" %><%--
--%><!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8">
  <title>indexページ</title>
 </head>
 <body> 
  <c:url value="/upload" var="upload" />
  <c:out value="${saveLocation}" /><br>
  <form action="${upload}" method="post" enctype="multipart/form-data">
   <input type="file" name="uploadFile"><br>
   <input type="submit" value="アップロード">
  </form>
 </body>
</html>
また、今回はServlet 3.0標準のファイルアップロードを使用するため、web.xmlに以下の記述が必要です。

dispatcher Servletにmultipart-config要素を追加します。

<servlet>
 <servlet-name>dispatcher</servlet-name>
 <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 <init-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>/WEB-INF/spring/beans-webmvc.xml</param-value>
 </init-param>
 <multipart-config>
  <max-file-size>10485760</max-file-size><!-- 10 MB -->
  <max-request-size>104857600</max-request-size><!-- 100 MB -->
  <file-size-threshold>1048576</file-size-threshold>
 </multipart-config>
</servlet>
また、Springの設定でmultipartResolverを定義する必要があります。これもServlet 3.0標準を使用するため以下のように記載します。

<bean id="multipartResolver"
 class="org.springframework.web.multipart.support.StandardServletMultipartResolver" />
アップロードされたファイルは、システムのテンポラリ領域にアップロードされたファイル名で格納します。同名ファイル等のチェックは特にしていません。アップロードがうまく行った場合には、元の画面にリダイレクトしメッセージを表示します。

アップロードのテストは以下のようになります。

@Test
public void slash_uploadへのPOST() throws Exception {
    byte[] fileImage = null;
    Path path = Paths.get("src/test/resources/kappa.jpg");
    if (Files.exists(path, LinkOption.NOFOLLOW_LINKS)) {
        fileImage = Files.readAllBytes(path);
    }

    // ローカルのファイル名もエミュレーションできる。
    String fileName = "かっぱ.jpg";
    MockMultipartFile file = new MockMultipartFile("uploadFile", fileName,
            null, fileImage);
    mockMvc.perform(fileUpload("/upload").file(file))
            .andExpect(redirectedUrl("/uploadForm"))
            .andExpect(
                    flash().attribute("saveLocation", endsWith("に保管しました。")));
    File actualFile = new File(System.getProperty("java.io.tmpdir"),
            fileName);

    // 画像保管されていることを確認する
    assertThat(actualFile.exists(), is(true));
    byte[] actualImage = Files.readAllBytes(Paths.get(actualFile.toURI()));
    assertThat(actualImage, is(equalTo(fileImage)));
}
ファイルのアップロードは実際にアップロードするファイルをMockMultipartFileで定義します。

用意したファイルのアップロードは、今までのgetメソッドやpostメソッドの代わりにfileUploadメソッドを使用して行います。fileUploadメソッドは、引数にURLを取り、MockMultipartHttpServletRequestBuilderのインスタンスを返します。

MockMultipartHttpServletRequestBuilderのfileメソッドでアップロードするファイルを指定します。引数に渡すのは、最初に用意したMockMultipartFileです。

アップロードされたファイルは、今回はローカルのテンポラリ領域に格納しているのでそのファイルとアップロードしたファイルを比較してアップロードの懸賞をしています。

まとめ

Spring MVCは3.1からしか知りませんが、3.2からテストが楽にかけると聞いて期待して待っていました。本格的に使いはじめる前にリリースされないかなあと思っていたので非常にラッキーです。今回紹介したのは、旧来のWebアプリの使い方に限定していますが、JSONやXMLをリクエストやレスポンスにとるようなものも簡単にテストができるようです（まだ試していません）。

兎にも角にもWebアプリのテストは悩ましい事が多いのでフレームワークとしてここま