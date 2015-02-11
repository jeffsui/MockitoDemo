  我们所有人在编写单元测试的时候面临的巨大挑战是模块对其他组件的依赖。同时花费大量的时间和精力去配置依赖的组件环境是一件出力不讨好的事情。使用Mock是一种有效地方式替代其他组件用来继续我们的单元测试构建过程。

接下来我将会展示一个使用mock技术的实例。这里我有一个数据访问层(简称DAL),建立一个类,提供应用程序访问和修改数据库中数据的基本API。接着我会测试这个DAL实例,但并不真正连接数据库。使用DAL类的目的是帮助我们隔离应用程序代码和数据显示层。

让我们通过下面的命令行创建一个java maven 项目。

<code><pre>
mvn archetype:generate -DgroupId=info.sanaulla -DartifactId=MockitoDemo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
</code></pre>

上述命令会创建一个名为MockitoDemo 的文件夹以及对应实体类的目录结构和测试文件。

请参考下面的例子来创建一个实体类:
<code><pre>
package info.sanaulla.models;

import java.util.List;

/**
* Model class for the book details.
*/
public class Book {

  private String isbn;
  private String title;
  private List<String> authors;
  private String publication;
  private Integer yearOfPublication;
  private Integer numberOfPages;
  private String image;

  public Book(String isbn,
              String title,
              List<String> authors,
              String publication,
              Integer yearOfPublication,
              Integer numberOfPages,
              String image){

    this.isbn = isbn;
    this.title = title;
    this.authors = authors;
    this.publication = publication;
    this.yearOfPublication = yearOfPublication;
    this.numberOfPages = numberOfPages;
    this.image = image;

  }

  public String getIsbn() {
    return isbn;
  }

  public String getTitle() {
    return title;
  }

  public List<String> getAuthors() {
    return authors;
  }

  public String getPublication() {
    return publication;
  }

  public Integer getYearOfPublication() {
    return yearOfPublication;
  }

  public Integer getNumberOfPages() {
    return numberOfPages;
  }

  public String getImage() {
    return image;
  }
}
</code></pre>

用来操作Book类的DAL类如下:
<code><pre>
package info.sanaulla.dal;

import info.sanaulla.models.Book;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

/**
* API layer for persisting and retrieving the Book objects.
*/
public class BookDAL {

  private static BookDAL bookDAL = new BookDAL();

  public List<Book> getAllBooks(){
      return Collections.EMPTY_LIST;
  }

  public Book getBook(String isbn){
      return null;
  }

  public String addBook(Book book){
      return book.getIsbn();
  }

  public String updateBook(Book book){
      return book.getIsbn();
  }

  public static BookDAL getInstance(){
      return bookDAL;
  }
}
</code></pre>

DAL 层现在并没有实际的功能,我将尝试用测试驱动开发的方式来构建([TDD][1])。我们并不关心,DAL层是通过实体关系映射来进行数据通信还是通过其他数据库API来实现上面的操作。

#数据驱动的数据访问层

有很多单元测试框架和mock框架,这里我选择Junit单元测试框架和moke框架Mockito。我们需要在pom.xml配置文件中更新对他们的依赖。

```
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 <a href="http://maven.apache.org/maven-v4_0_0.xsd">http://maven.apache.org/maven-v4_0_0.xsd"</a>>
  <modelVersion>4.0.0</modelVersion>
  <groupId>info.sanaulla</groupId>
  <artifactId>MockitoDemo</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>MockitoDemo</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <!-- Dependency for JUnit -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.10</version>
      <scope>test</scope>
    </dependency>
    <!-- Dependency for Mockito -->
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-all</artifactId>
      <version>1.9.5</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```




现在我们编写BookDAL的单元测试类。通过单元测试,我们可以在BookDAL中注入mock对象进行单元测试而不用依赖其他数据源。

首先建立一个空的测试类,代码如下:
<code><pre>
public class BookDALTest {
  public void setUp() throws Exception {

  }

  public void testGetAllBooks() throws Exception {

  }

  public void testGetBook() throws Exception {

  }

  public void testAddBook() throws Exception {

  }

  public void testUpdateBook() throws Exception {

  }
}
</code></pre>

下面的代码中,我们在setUp方法中通过mock方式注入BookDAL对象
<code><pre>
public class BookDALTest {

  private static BookDAL mockedBookDAL;
  private static Book book1;
  private static Book book2;

  @BeforeClass
  public static void setUp(){
    //Create mock object of BookDAL
    mockedBookDAL = mock(BookDAL.class);

    //Create few instances of Book class.
    book1 = new Book("8131721019","Compilers Principles",
            Arrays.asList("D. Jeffrey Ulman","Ravi Sethi", "Alfred V. Aho", "Monica S. Lam"),
            "Pearson Education Singapore Pte Ltd", 2008,1009,"BOOK_IMAGE");

    book2 = new Book("9788183331630","Let Us C 13th Edition",
            Arrays.asList("Yashavant Kanetkar"),"BPB PUBLICATIONS", 2012,675,"BOOK_IMAGE");

    //Stubbing the methods of mocked BookDAL with mocked data. 
    when(mockedBookDAL.getAllBooks()).thenReturn(Arrays.asList(book1, book2));
    when(mockedBookDAL.getBook("8131721019")).thenReturn(book1);
    when(mockedBookDAL.addBook(book1)).thenReturn(book1.getIsbn());
    when(mockedBookDAL.updateBook(book1)).thenReturn(book1.getIsbn());
  }

  public void testGetAllBooks() throws Exception {}

  public void testGetBook() throws Exception {}

  public void testAddBook() throws Exception {}

  public void testUpdateBook() throws Exception {}
}
</code></pre>

在setUp方法中我通过下面这种方式

1.创建BookDAL实例:
<code><pre>
BookDAL mockedBookDAL = mock(BookDAL.class);
</code></pre>

 2.Mokito API中DAL的mock对象,使用when方法接收调用mock对象的方法返回值参数。

<code><pre>
//When getAllBooks() is invoked then return the given data and so on for the other methods.
when(mockedBookDAL.getAllBooks()).thenReturn(Arrays.asList(book1, book2));
when(mockedBookDAL.getBook("8131721019")).thenReturn(book1);
when(mockedBookDAL.addBook(book1)).thenReturn(book1.getIsbn());
when(mockedBookDAL.updateBook(book1)).thenReturn(book1.getIsbn());
</code></pre>


实现其他的已经声明的测试方法：
<code><pre>
package info.sanaulla.dal;

import info.sanaulla.models.Book;
import org.junit.BeforeClass;
import org.junit.Test;

import static org.junit.Assert.*;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import java.util.Arrays;
import java.util.List;


public class BookDALTest {

  private static BookDAL mockedBookDAL;
  private static Book book1;
  private static Book book2;

  @BeforeClass
  public static void setUp(){
    mockedBookDAL = mock(BookDAL.class);
    book1 = new Book("8131721019","Compilers Principles",
            Arrays.asList("D. Jeffrey Ulman","Ravi Sethi", "Alfred V. Aho", "Monica S. Lam"),
            "Pearson Education Singapore Pte Ltd", 2008,1009,"BOOK_IMAGE");

    book2 = new Book("9788183331630","Let Us C 13th Edition",
            Arrays.asList("Yashavant Kanetkar"),"BPB PUBLICATIONS", 2012,675,"BOOK_IMAGE");

    when(mockedBookDAL.getAllBooks()).thenReturn(Arrays.asList(book1, book2));
    when(mockedBookDAL.getBook("8131721019")).thenReturn(book1);
    when(mockedBookDAL.addBook(book1)).thenReturn(book1.getIsbn());

    when(mockedBookDAL.updateBook(book1)).thenReturn(book1.getIsbn());

  }

  @Test
  public void testGetAllBooks() throws Exception {

    List<Book> allBooks = mockedBookDAL.getAllBooks();
    assertEquals(2, allBooks.size());
    Book myBook = allBooks.get(0);
    assertEquals("8131721019", myBook.getIsbn());
    assertEquals("Compilers Principles", myBook.getTitle());
    assertEquals(4, myBook.getAuthors().size());
    assertEquals((Integer)2008, myBook.getYearOfPublication());
    assertEquals((Integer) 1009, myBook.getNumberOfPages());
    assertEquals("Pearson Education Singapore Pte Ltd", myBook.getPublication());
    assertEquals("BOOK_IMAGE", myBook.getImage());
  }

  @Test
  public void testGetBook(){
    String isbn = "8131721019";
    Book myBook = mockedBookDAL.getBook(isbn);

    assertNotNull(myBook);
    assertEquals(isbn, myBook.getIsbn());
    assertEquals("Compilers Principles", myBook.getTitle());
    assertEquals(4, myBook.getAuthors().size());
    assertEquals("Pearson Education Singapore Pte Ltd", myBook.getPublication());
    assertEquals((Integer)2008, myBook.getYearOfPublication());
    assertEquals((Integer)1009, myBook.getNumberOfPages());

  }

  @Test
  public void testAddBook(){
    String isbn = mockedBookDAL.addBook(book1);
    assertNotNull(isbn);
    assertEquals(book1.getIsbn(), isbn);
  }

  @Test
  public void testUpdateBook(){
    String isbn = mockedBookDAL.updateBook(book1);
    assertNotNull(isbn);
    assertEquals(book1.getIsbn(), isbn);
  }
}
</code></pre>

然后我们通过maven的命令航方式:`mvn test`运行当前的项目。控制台输出如下:
<code><pre>
\-------------------------------------------------------
 T E S T S
\-------------------------------------------------------
Running info.sanaulla.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.029 sec
Running info.sanaulla.dal.BookDALTest
Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.209 sec

Results :

Tests run: 5, Failures: 0, Errors: 0, Skipped: 0
</code></pre>

现在我们就实现了即使不配置数据源方式,而使用mock对象来测试DAL类。

#实例代码

网盘地址:[MockitoDemo][2]


  [1]: http://blog.sanaulla.info/2012/04/28/test-driven-development-my-thoughts/
  [2]: http://pan.baidu.com/s/1kT1MrPX