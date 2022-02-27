# Design Pattern
- test 

```mermaid
  graph TD;
      A-->B;
      A-->C;
      B-->D;
      C-->D;
```


## Singleton Pattern

### What is?

- 개발하고자 하는 시스템에서 하나의 클래스 인스턴스를 보장해줌

### 왜 사용함?

- 클랙스에서 만들 수 있는 인스턴스가 오직 하나이고 이에대한 접근도 오직 하나로만 통일
- 여러 번 생성되어서 각각 다른 값을 가질 필요가 없는 경우에 사용
  - eg., Connection Pool을 사용한다면 굳이 여러 개를 만들 필요가 있을 것인가?

### Code?

```java
public class Main {
    public static void main(String[] args) {
        ConnectionPool instance1 = ConnectionPool.getInstance();
        ConnectionPool instance2 = ConnectionPool.getInstance();

        // 항상 null을 return
        System.out.println(instance1 == instance2);
    }
}

class ConnectionPool {
    private static ConnectionPool instance = new ConnectionPool();

    // 외부에서 생성할 수 없도록 private 으로 생성자를 만듦
    private ConnectionPool() {
    }

    public static ConnectionPool getInstance() {
        // null은 아니지만(기본적으로 이미 `instance`를 선언함, 방어코드로 생성
        if (instance == null) {
            instance = new ConnectionPool();
        }
        return instance;
    }
}
```

## Prototype Pattern

### What is?

- 복제해서 인스턴스를 생성하는 패턴

### 복제를 왜 하는데?

- 생성 과정이 복잡한 경우가 있음
  - 여러 개의 조합에 의해 생성되어야 함
    - eg, DB에서 정보를 가져와서 리스트를 생성해야하는 경우 등
      - 견본을 하나 만들어두고 복제를 해서 사용을 하려고 함

- 복제를 할 수 있도록
  - `clone` override
  - implements `Cloneable` -> 복제가 가능하도록 설정
  - 기본적으로 복제하는 건(`clone` method) -> Depp Copy가 아님, Shallow Copy
    -  주소를 복제해주는 것이기 때문에 새로운 복제본이 나오는 것은 아님

### Code?

- deep copy -> 객체를 새로 생성해서 진행

```java
    @Override
    protected Object clone() throws CloneNotSupportedException {
                BookShelf bs = new BookShelf();
            for(Book book : shelf){
                    bs.addBook(book);
        }
      return bs;
        //return super.clone();
    }
```

- shallow copy

```java
public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        BookShelf bs = new BookShelf();
        bs.addBook(new Book("정래", "A"));
        bs.addBook(new Book("올래", "B"));
        bs.addBook(new Book("갈래", "C"));


        System.out.println(bs.toString());
        BookShelf anonther = (BookShelf) bs.clone();
        System.out.println(anonther);
    }
}

class Book{
    private String author;
    private String title;

    public Book(String author, String title) {
        this.author = author;
        this.title = title;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    @Override
    public String toString() {
        return author + "," + title;
    }
}

class BookShelf implements Cloneable{
    private ArrayList<Book> shelf;

    public BookShelf() {
        this.shelf = new ArrayList<>();
    }

    public ArrayList<Book> getShelf() {
        return shelf;
    }

    public void setShelf(ArrayList<Book> shelf) {
        this.shelf = shelf;
    }

    public void addBook(Book book) {
        shelf.add(book);
    }

    @Override
    public String toString() {
        return shelf.toString();
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```
