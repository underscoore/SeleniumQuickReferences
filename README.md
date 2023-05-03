# Learn Automation Testing with Java and Selenium Quick Reference

### References

#### Selenium Standalone

- Manual Installation - https://github.com/lmc-eu/steward/wiki/Selenium-server-&-browser-drivers
- Automated Installation - https://www.npmjs.com/package/selenium-standalone
- URL - http://localhost:4444/wd/hub

##### Installation and Launch

- Step I : Install NPM
- Step II : Install selenium-standalone
Terminal or Command Prompt
```
# In Windows, Run CMD as Administrator

npm install selenium-standalone@latest -g
# If need use sudo npm install selenium-standalone@latest -g

selenium-standalone install
# if needed use sudo 
```
- Step III : Launch Selenium Standalone
```
selenium-standalone start
```

> By default, google chrome, firefox and phantomjs are available when installed on the host system
```
# install a single driver within the default list (chrome, ie, edge, firefox) 
selenium-standalone install --singleDriverInstall=chrome
```

Reference 
- More Options - https://www.npmjs.com/package/selenium-standalone#command-line-interface

#### Selenium Grid
- URL -http://localhost:4444/grid/console

##### Installation and Launch
- Follow Step I and II of Selenium Standalone
- Step III

```
selenium-standalone start -- -role hub
selenium-standalone start -- -role node -hub http://localhost:4444/grid/register
selenium-standalone start -- -role node -hub http://localhost:4444/grid/register -port 5556

selenium-standalone start -- -role node -hub http://localhost:4444/grid/register -browser browserName=firefox

selenium-andalone start -- -role node -hub http://localhost:4444/grid/register -port 5556 -browser browserName=chrome,maxInstances=2
```

#### Code Snippets

##### Maven Dependencies

###### JUnit

```xml
  <dependencies>
  
    <dependency>
      <groupId>org.seleniumhq.selenium</groupId>
      <artifactId>selenium-java</artifactId>
      <version>3.13.0</version>
      <scope>test</scope>
    </dependency>

  
    <!-- https://github.com/bonigarcia/webdrivermanager -->
    <dependency>
      <groupId>io.github.bonigarcia</groupId>
      <artifactId>webdrivermanager</artifactId>
      <version>2.2.4</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.0.13</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  
  </dependencies>
```
###### Test NG

```xml
  <dependencies>

    <dependency>
      <groupId>org.seleniumhq.selenium</groupId>
      <artifactId>selenium-java</artifactId>
      <version>3.13.0</version>
      <scope>test</scope>
    </dependency>

    <!-- https://github.com/bonigarcia/webdrivermanager -->
    <dependency>
      <groupId>io.github.bonigarcia</groupId>
      <artifactId>webdrivermanager</artifactId>
      <version>2.2.4</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.0.13</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.testng</groupId>
      <artifactId>testng</artifactId>
      <version>6.14.3</version>
      <scope>test</scope>
    </dependency>

  </dependencies>

```

###### Other Dependencies

```xml
    <dependency>
      <groupId>org.assertj</groupId>
      <artifactId>assertj-core</artifactId>
      <version>3.10.0</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>com.opencsv</groupId>
      <artifactId>opencsv</artifactId>
      <version>3.4</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi</artifactId>
      <version>3.6</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi-ooxml</artifactId>
      <version>3.6</version>
      <scope>test</scope>
    </dependency>

```

### Web DriverManager instance for different browsers
##### Java Code

###### Chrome Driver

```java
  ChromeDriverManager.getInstance().setup();
  driver = new ChromeDriver();
```

###### Firefox Driver

```java
  FirefoxDriverManager.getInstance().setup();
  driver = new FirefoxDriver();
```

###### Excel Data Reader
```java
package com.in28minutes.datadriventests;

import java.io.File;

import org.apache.poi.openxml4j.opc.OPCPackage;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.ss.usermodel.WorkbookFactory;

public class ExcelReadUtil {
  public static String[][] readExcelInto2DArray(String excelFilePath, String sheetName, int totalCols) {

    File file = new File(excelFilePath);

    String[][] tabArray = null;

    try {
      OPCPackage opcPackage = OPCPackage.open(file.getAbsolutePath());

      Workbook wb = WorkbookFactory.create(opcPackage);

      Sheet sheet = wb.getSheet(sheetName);

      int totalRows = sheet.getLastRowNum() + 1;

      tabArray = new String[totalRows][totalCols];

      for (int i = 0; i < totalRows; i++) {
        for (int j = 0; j < totalCols; j++) {
          Cell cell = sheet.getRow(i).getCell(j);
          System.out.println(cell + " " + i + " " + j);

          if (cell == null)
            continue;

          switch (cell.getCellType()) {
          case Cell.CELL_TYPE_BOOLEAN:
            tabArray[i][j] = String.valueOf(cell.getBooleanCellValue());
            break;
          case Cell.CELL_TYPE_NUMERIC:
            tabArray[i][j] = String.valueOf(cell.getNumericCellValue());
            break;
          case Cell.CELL_TYPE_STRING:
            tabArray[i][j] = cell.getStringCellValue();
            break;
          default:
            tabArray[i][j] = "";
            break;
          }
        }
      }
    } catch (Exception e) {
      e.printStackTrace();
      throw new RuntimeException(e);
    }

    return tabArray;
  }

}

```


##### TestNG (testng.xml)

```
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" >
<suite name="First Suite" verbose="1">

<test name="RegressionOnChrome">
  <parameter name="browser" value="chrome" />
  <packages>
    <package name="com.test.advance.*" />
  </packages>
</test>
<test name="RegressionOnFirefox">
  <parameter name="browser" value="firefox"></parameter>
  <packages>
    <package name="com.test.advance.*"></package>
  </packages>
</test>
</suite>


```

##### AbstractChromeWebDriverTest / Base class Reference

```java
package com.in28minutes.webdriver.basics;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterTest;
import org.testng.annotations.BeforeTest;

import io.github.bonigarcia.wdm.WebDriverManager;

public abstract class AbstractChromeWebDriverTest {

  protected WebDriver driver;

  public AbstractChromeWebDriverTest() {
    super();
  }

  @BeforeTest
  public void beforeTest() {
    //Download the web driver executable
    WebDriverManager.chromedriver().setup();
    
    //Create a instance of your web driver - chrome
    driver = new ChromeDriver();
  }

  @AfterTest
  public void afterTest() {
    driver.quit();
  }
  
  public void sleep(int seconds) {
    try {
      Thread.sleep(seconds * 1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }

}
```

#### Resources

##### Plugins
- Eclipse Plugin for TestNg - http://beust.com/eclipse

##### Selenium IDE
- Selenium IDE will no longer work from Firefox 55
   - https://seleniumhq.wordpress.com/2017/08/09/firefox-55-and-selenium-ide/
- A new version of Selenium IDE for Selenium 3 is being built
   - https://seleniumhq.wordpress.com/2018/08/06/selenium-ide-tng/

##### Troubleshooting
- Troubleshooting Guide - https://github.com/in28minutes/in28minutes-initiatives/tree/master/The-in28Minutes-TroubleshootingGuide-And-FAQ
- Troubleshooting Guide for Maven Issues - https://github.com/in28minutes/in28minutes-initiatives/tree/master/The-in28Minutes-TroubleshootingGuide-And-FAQ#error---you-are-not-using-a-jdk

##### Browsers
- WebDriver Specification - https://www.w3.org/TR/webdriver/
- Firefox version 47.0+ Geckodriver is needed to interact with Firefox - Similar to Chrome.
- https://ftp.mozilla.org/pub/firefox/releases/61.0.1/
- https://ftp.mozilla.org/pub/firefox/releases/54.0.1/





 ### Graphviz
 
 ```
 digraph G {
  color="#1BA84A";//green
  color="#D14D28";//orange
  color="#59C8DE";//blue

  node[style=filled,color="#59C8DE"]
  
  subgraph cluster_0 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    Code[label=<Automation Code + Libraries<BR />
    <FONT POINT-SIZE="10">Java, Python etc</FONT>>];
    WebDriver[label=<Web Driver<BR />
    <FONT POINT-SIZE="10">Chrome Driver, Firefox Driver,<BR /> Safari Driver, IE Driver etc</FONT>>];
    Browser[label=<Browser<BR />
        <FONT POINT-SIZE="10">Chrome, Firefox, Safari, IE etc</FONT>>];
    Code -> WebDriver -> Browser;
    label = "Single    System";
  }
    
  RunTests[label=<Run Automation Tests<BR />
        <FONT POINT-SIZE="10">Manually, Continuous Integration etc</FONT>>];
  Application[label=<Web Application<BR />
        <FONT POINT-SIZE="10">Todo Management, Google, Facebook etc</FONT>>];

  RunTests -> Code;
  Browser -> Application;

  RunTests [shape=Mdiamond];
  Application [shape=rectangle];

}

digraph SeleniumStandAlone {
  color="#1BA84A";//green
  color="#D14D28";//orange
  color="#59C8DE";//blue

  node[style=filled,color="#59C8DE"]
  
      subgraph cluster_2 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    WebDriver[label=<Web Driver<BR />
    <FONT POINT-SIZE="10">Chrome Driver, Firefox Driver,<BR /> Safari Driver, IE Driver etc</FONT>>];
    Browser[label=<Browser<BR />
        <FONT POINT-SIZE="10">Chrome, Firefox, Safari, IE etc</FONT>>];
    StandaloneServer[];
    StandaloneServer -> WebDriver -> Browser;
    label = "Stand Alone Server";
  }

  subgraph cluster_0 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    Code[label=<Automation Code + Libraries<BR />
    <FONT POINT-SIZE="10">Java, Python etc</FONT>>];
    Code -> StandaloneServer
    label = "System 1";
  }
    
  subgraph cluster_1 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    Code1[label=<Automation Code + Libraries<BR />
    <FONT POINT-SIZE="10">Java, Python etc</FONT>>];
    Code1 -> StandaloneServer
    label = "System 2";
  }

 
  Application[label=<Web Application<BR />
        <FONT POINT-SIZE="10">Todo Management, Google, Facebook etc</FONT>>];

  
  Browser -> Application;

  Application [shape=rectangle];

}

digraph SeleniumGrid {
  color="#1BA84A";//green
  color="#D14D28";//orange
  color="#59C8DE";//blue


  node[style=filled,color="#59C8DE"]
  
        subgraph cluster_3 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    WebDriver[label=<Web Driver<BR />
    <FONT POINT-SIZE="10">Chrome Driver, Firefox Driver,<BR /> Safari Driver, IE Driver etc</FONT>>];
    Browser[label=<Browser<BR />
        <FONT POINT-SIZE="10">Chrome, Firefox, Safari, IE etc</FONT>>];
    SeleniumNode1[label="Selenium Node"];
    SeleniumNode1 -> WebDriver -> Browser;
    label = "Selenium Node 1";
  }

      subgraph cluster_4 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    WebDriver2[label=<Web Driver<BR />
    <FONT POINT-SIZE="10">Chrome Driver, Firefox Driver,<BR /> Safari Driver, IE Driver etc</FONT>>];
    Browser2[label=<Browser<BR />
        <FONT POINT-SIZE="10">Chrome, Firefox, Safari, IE etc</FONT>>];
    SeleniumNode2[label="Selenium Node"];
    SeleniumNode2 -> WebDriver2 -> Browser2;
    label = "Selenium Node 2";
  }
  

      subgraph cluster_2 {
    style=filled;
    color="#59C8DE";
    
    node [style=filled,color="#D14D28", fontcolor=white];
    WebDriver[label=<Web Driver<BR />
    <FONT POINT-SIZE="10">Chrome Driver, Firefox Driver,<BR /> Safari Driver, IE Driver etc</FONT>>];
    Browser[label=<Browser<BR />
        <FONT POINT-SIZE="10">Chrome, Firefox, Safari, IE etc</FONT>>];
        
    SeleniumGrid -> SeleniumNode1;
    SeleniumGrid -> SeleniumNode2;
    label = "Selenium Grid";
  }


    
  Application[label=<Web Application<BR />
        <FONT POINT-SIZE="10">Todo Management, Google, Facebook etc</FONT>>];

  AutomationCode[label=<Automation Code + Libraries<BR />
        <FONT POINT-SIZE="10">Java, Python etc, Application 1..n etc, System 1..n etc </FONT>>];
  
  AutomationCode -> SeleniumGrid;
  Browser -> Application;
  Browser2 -> Application;

  Application [shape=rectangle];
  AutomationCode [shape=Mdiamond];
}


```

### Backup

#### JUnit + Selenium - Google Export from Katalon

```
package com.example.tests;

import java.util.regex.Pattern;
import java.util.concurrent.TimeUnit;
import org.junit.*;
import static org.junit.Assert.*;
import static org.hamcrest.CoreMatchers.*;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.Select;

public class GoogleSearchForIn28minutes {
  private WebDriver driver;
  private String baseUrl;
  private boolean acceptNextAlert = true;
  private StringBuffer verificationErrors = new StringBuffer();

  @Before
  public void setUp() throws Exception {
    driver = new FirefoxDriver();
    baseUrl = "https://www.katalon.com/";
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
  }

  @Test
  public void testGoogleSearchForIn28minutes() throws Exception {
    driver.get("https://www.google.com/");
    driver.findElement(By.id("lst-ib")).click();
    driver.findElement(By.id("lst-ib")).clear();
    driver.findElement(By.id("lst-ib")).sendKeys("in28minutes");
    driver.findElement(By.id("lst-ib")).sendKeys(Keys.ENTER);
  }

  @After
  public void tearDown() throws Exception {
    driver.quit();
    String verificationErrorString = verificationErrors.toString();
    if (!"".equals(verificationErrorString)) {
      fail(verificationErrorString);
    }
  }

  private boolean isElementPresent(By by) {
    try {
      driver.findElement(by);
      return true;
    } catch (NoSuchElementException e) {
      return false;
    }
  }

  private boolean isAlertPresent() {
    try {
      driver.switchTo().alert();
      return true;
    } catch (NoAlertPresentException e) {
      return false;
    }
  }

  private String closeAlertAndGetItsText() {
    try {
      Alert alert = driver.switchTo().alert();
      String alertText = alert.getText();
      if (acceptNextAlert) {
        alert.accept();
      } else {
        alert.dismiss();
      }
      return alertText;
    } finally {
      acceptNextAlert = true;
    }
  }
}
```

#### JUnit + Selenium - Facebook Export from Katalon

```
package com.example.tests;

import java.util.regex.Pattern;
import java.util.concurrent.TimeUnit;
import org.junit.*;
import static org.junit.Assert.*;
import static org.hamcrest.CoreMatchers.*;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.Select;

public class FacebookLogin {
  private WebDriver driver;
  private String baseUrl;
  private boolean acceptNextAlert = true;
  private StringBuffer verificationErrors = new StringBuffer();

  @Before
  public void setUp() throws Exception {
    driver = new FirefoxDriver();
    baseUrl = "https://www.katalon.com/";
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
  }

  @Test
  public void testFacebookLogin() throws Exception {
    driver.get("https://www.facebook.com/");
    driver.findElement(By.id("email")).click();
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("in28minutes");
    driver.findElement(By.id("pass")).clear();
    driver.findElement(By.id("pass")).sendKeys("dummy");
    driver.findElement(By.id("pass")).sendKeys(Keys.ENTER);
  }

  @After
  public void tearDown() throws Exception {
    driver.quit();
    String verificationErrorString = verificationErrors.toString();
    if (!"".equals(verificationErrorString)) {
      fail(verificationErrorString);
    }
  }

  private boolean isElementPresent(By by) {
    try {
      driver.findElement(by);
      return true;
    } catch (NoSuchElementException e) {
      return false;
    }
  }

  private boolean isAlertPresent() {
    try {
      driver.switchTo().alert();
      return true;
    } catch (NoAlertPresentException e) {
      return false;
    }
  }

  private String closeAlertAndGetItsText() {
    try {
      Alert alert = driver.switchTo().alert();
      String alertText = alert.getText();
      if (acceptNextAlert) {
        alert.accept();
      } else {
        alert.dismiss();
      }
      return alertText;
    } finally {
      acceptNextAlert = true;
    }
  }
}

```

#### TestNg + Selenium - Google Export from Katalon

```
package com.example.tests;

import java.util.regex.Pattern;
import java.util.concurrent.TimeUnit;
import org.testng.annotations.*;
import static org.testng.Assert.*;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.Select;

public class GoogleSearchForIn28minutes {
  private WebDriver driver;
  private String baseUrl;
  private boolean acceptNextAlert = true;
  private StringBuffer verificationErrors = new StringBuffer();

  @BeforeClass(alwaysRun = true)
  public void setUp() throws Exception {
    driver = new FirefoxDriver();
    baseUrl = "https://www.katalon.com/";
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
  }

  @Test
  public void testGoogleSearchForIn28minutes() throws Exception {
    driver.get("https://www.google.com/");
    driver.findElement(By.id("lst-ib")).click();
    driver.findElement(By.id("lst-ib")).clear();
    driver.findElement(By.id("lst-ib")).sendKeys("in28minutes");
    driver.findElement(By.id("lst-ib")).sendKeys(Keys.ENTER);
  }

  @AfterClass(alwaysRun = true)
  public void tearDown() throws Exception {
    driver.quit();
    String verificationErrorString = verificationErrors.toString();
    if (!"".equals(verificationErrorString)) {
      fail(verificationErrorString);
    }
  }

  private boolean isElementPresent(By by) {
    try {
      driver.findElement(by);
      return true;
    } catch (NoSuchElementException e) {
      return false;
    }
  }

  private boolean isAlertPresent() {
    try {
      driver.switchTo().alert();
      return true;
    } catch (NoAlertPresentException e) {
      return false;
    }
  }

  private String closeAlertAndGetItsText() {
    try {
      Alert alert = driver.switchTo().alert();
      String alertText = alert.getText();
      if (acceptNextAlert) {
        alert.accept();
      } else {
        alert.dismiss();
      }
      return alertText;
    } finally {
      acceptNextAlert = true;
    }
  }
}
```

#### TestNg + Selenium - Facebook Export from Katalon

```
package com.example.tests;

import java.util.regex.Pattern;
import java.util.concurrent.TimeUnit;
import org.testng.annotations.*;
import static org.testng.Assert.*;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.Select;

public class FacebookLogin {
  private WebDriver driver;
  private String baseUrl;
  private boolean acceptNextAlert = true;
  private StringBuffer verificationErrors = new StringBuffer();

  @BeforeClass(alwaysRun = true)
  public void setUp() throws Exception {
    driver = new FirefoxDriver();
    baseUrl = "https://www.katalon.com/";
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
  }

  @Test
  public void testFacebookLogin() throws Exception {
    driver.get("https://www.facebook.com/");
    driver.findElement(By.id("email")).click();
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("in28minutes");
    driver.findElement(By.id("pass")).clear();
    driver.findElement(By.id("pass")).sendKeys("dummy");
    driver.findElement(By.id("pass")).sendKeys(Keys.ENTER);
  }

  @AfterClass(alwaysRun = true)
  public void tearDown() throws Exception {
    driver.quit();
    String verificationErrorString = verificationErrors.toString();
    if (!"".equals(verificationErrorString)) {
      fail(verificationErrorString);
    }
  }

  private boolean isElementPresent(By by) {
    try {
      driver.findElement(by);
      return true;
    } catch (NoSuchElementException e) {
      return false;
    }
  }

  private boolean isAlertPresent() {
    try {
      driver.switchTo().alert();
      return true;
    } catch (NoAlertPresentException e) {
      return false;
    }
  }

  private String closeAlertAndGetItsText() {
    try {
      Alert alert = driver.switchTo().alert();
      String alertText = alert.getText();
      if (acceptNextAlert) {
        alert.accept();
      } else {
        alert.dismiss();
      }
      return alertText;
    } finally {
      acceptNextAlert = true;
    }
  }
}

```




#### Text search with contains
```java
WebElement notifications = driver.findElement(By.xpath("//*[contains(text(),'" + textToSearchFor + "')]"));
System.out.println("NOTIFICATIONS : " + notifications.getText());
```

#### Advanced CSS Selectors

```
attributeSuffix - driver.findElement(By.cssSelector("input[name$='word']"));
sibling - driver.findElement(By.cssSelector("input[name='password'] + input[type='submit']"));
directDescendant - driver.findElement(By.cssSelector("div > input[name='email']"));
anyDescendant() - driver.findElement(By.cssSelector("form input[name='email']"));
        attributePrefix - driver.findElement(By.cssSelector("input[name^='pass']"));

```

#### Advanced XPAth Selectors

```
CLASS - //*[contains(concat(' ',normalize-space(@class),' '),' btn ')]
driver.findElement(By.xpath("//div[contains(.,'A visible paragraph')]/form"));
driver.findElement(By.xpath("//*[contains(text(),'A paragraph XXX with this text in bold')]"));
driver.findElement(By.xpath("//*[contains(normalize-space(.),'A paragraph with this text in bold')]"));
```


#### File Upload
```
  @Test
  public void testFileUpload() throws IOException {

    ChromeDriverManager.getInstance().setup();

    WebDriver driver = new ChromeDriver();

    Path fileToUpload = Files.createTempFile(Paths.get("."), "some-file-to-upload", ".txt");

    driver.get("http://localhost:8080/pages/file-upload.html");

    driver.findElement(By.name("file")).sendKeys(fileToUpload.toFile().getCanonicalPath());

    driver.findElement(By.cssSelector("input[type='submit']")).click();

    String message = driver.findElement(By.id("welcome-message")).getText();
    System.out.println(message);
    
    Files.delete(fileToUpload);
    driver.close();

    driver.quit();

  }
```

### Advanced Selenium Listeners

#### /src/test/java/com/in28minutes/automation/webapp/basics/WebDriverEventListenerUsingImplements.java

```java
public class WebDriverEventListenerUsingImplements implements WebDriverEventListener{

  @Override
  public void afterClickOn(WebElement element, WebDriver driver) {
    System.out.printf("Element with tag %s and name %s is clicked \n",  element.getTagName(), element.getAttribute("name"));
    
  }

  //Other empty methods are deleted for saving space!!

}
```

#### Unit Test

```java
  @Test
  public void setFormElementsWithListeners() {

    ChromeDriverManager.getInstance().setup();

    WebDriver driver = new ChromeDriver();

    EventFiringWebDriver eventFiringDriver = new EventFiringWebDriver(driver);

    WebDriverEventListenerUsingImplements eventListener = new WebDriverEventListenerUsingImplements();

    eventFiringDriver.register(eventListener);

    eventFiringDriver.get("http://localhost:8080/pages/forms.html");

    driver.findElement(By.id("textElement")).sendKeys("new-textElement-value");
    driver.findElement(By.id("textAreaElement")).sendKeys("new-textAreaElement-value");

    eventFiringDriver.findElement(By.id("checkboxElement1")).click();
    eventFiringDriver.findElement(By.id("checkboxElement2")).click();

    driver.findElement(By.id("inlineCheckboxElement1")).click();
    driver.findElement(By.id("inlineCheckboxElement2")).click();

    List<WebElement> optionRadios = driver.findElements(By.name("optionsRadios"));

    optionRadios.get(1).click();

    List<WebElement> optionsRadiosInline = driver.findElements(By.name("optionsRadiosInline"));

    optionsRadiosInline.get(1).click();

    Select selectElement = new Select(driver.findElement(By.id("selectElement1")));// 1

    selectElement.selectByValue("4");

    Select multiSelectElement = new Select(driver.findElement(By.id("multiSelectElement")));// 1,3

    multiSelectElement.selectByValue("5");

    driver.close();
    driver.quit();
  }
```

#### /src/test/java/com/in28minutes/automation/webapp/basics/WebDriverEventListenerUsingExtends.java

```java
package com.in28minutes.automation.webapp.basics;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.events.AbstractWebDriverEventListener;

public class WebDriverEventListenerUsingExtends extends AbstractWebDriverEventListener{
  
  @Override
  public void beforeNavigateTo(String url, WebDriver driver) {
      System.out.printf("We are at %s and we are navigating to %s \n",  driver.getCurrentUrl(), url);
  }

  @Override
  public void afterNavigateTo(String url, WebDriver driver) {
    System.out.printf("We are at %s and we have navigated to %s \n",  driver.getCurrentUrl(), url);
  }

  @Override
  public void beforeNavigateBack(WebDriver driver) {
    System.out.printf("We are at %s and we want to navigate back \n",  driver.getCurrentUrl());
  }

  @Override
  public void afterNavigateBack(WebDriver driver) {
    System.out.printf("We are at %s and we completed the navigate back \n",  driver.getCurrentUrl());
  }

  @Override
  public void afterClickOn(WebElement element, WebDriver driver) {
    System.out.printf("Element with tag %s and name %s is clicked \n",  element.getTagName(), element.getAttribute("name"));
  }

}
```

## Test NG Advanced Features

```java

//@Test(groups={"group-4"})
public class PlayingWithTestNGTest {

  @Test(groups = { "group1" })
  //Groups can be Unit Test, Integration Test, Performance etc
  public void group1Test() {
    System.out.println("Group 1 Test");
  }

  @Test(groups = { "group2" })
  //Groups can be Unit Test, Integration Test etc
  public void group2Test() {
    System.out.println("Group 2 Test");
  }

  @AfterGroups(groups = { "group1" })
  public void afterGroup1() {
    System.out.println("After Group1");
  }

  @BeforeGroups(groups = { "group2" })
  public void beforeGroup2() {
    System.out.println("Before Group2");
  }

  @Test(timeOut = 1000)
  public void timeoutTest() {

  }

  @Test(expectedExceptions = { Exception.class })
  public void expectAnException() {
    throw new RuntimeException("flkasdjf");
  }

  @Test(enabled = false)
  public void ignoredTest() {

  }

  @Test
  @Parameters({ "browser" })
  public void browserSpecificTest(@Optional("firefox") String browser) {
    System.out.println(browser);
  }

  @Test(dependsOnMethods="setupSomething")
  //dependsOnGroups
  public void thisTestNeedsSomethingSetup() {
    System.out.println("I need something else");
  }
  
  @Test
  public void setupSomething() {
    System.out.println("Setup Something");    
  }
```

### /src/test/java/com/in28minutes/automation/TestNgResultListener.java

```java
package com.in28minutes.automation;

import org.testng.ITestContext;
import org.testng.ITestListener;
import org.testng.ITestResult;

public class TestNgResultListener implements ITestListener{

  public void onFinish(ITestContext arg0) {
    // TODO Auto-generated method stub
    
  }

  public void onStart(ITestContext arg0) {
    // TODO Auto-generated method stub
    
  }

  public void onTestFailedButWithinSuccessPercentage(ITestResult arg0) {
    // TODO Auto-generated method stub
    
  }

  public void onTestFailure(ITestResult arg0) {
    System.out.println("Test Failed");
    
  }

  public void onTestSkipped(ITestResult arg0) {
    // TODO Auto-generated method stub
    
  }

  public void onTestStart(ITestResult arg0) {
    // TODO Auto-generated method stub
    
  }

  public void onTestSuccess(ITestResult arg0) {
    System.out.println("Test Succeded");
    
  }

}
```
---

### /src/test/java/com/in28minutes/automation/TestNgTestReporter.java

```java
package com.in28minutes.automation;

import java.util.List;
import java.util.Map;

import org.testng.IReporter;
import org.testng.ISuite;
import org.testng.ISuiteResult;
import org.testng.ITestContext;
import org.testng.xml.XmlSuite;

public class TestNgTestReporter implements IReporter {

  public void generateReport(List<XmlSuite> xmlSuite, List<ISuite> iSuite, String outputDirectory) {
    for(ISuite suite: iSuite) {
      Map<String, ISuiteResult> results = suite.getResults();
      for(ISuiteResult result:results.values()) {
        ITestContext testContext = result.getTestContext();
        System.out.println(testContext.getPassedTests());
      }
    }
    
  }

}
```
