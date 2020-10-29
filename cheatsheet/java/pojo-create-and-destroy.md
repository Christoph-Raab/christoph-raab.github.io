## Statische Factory Methoden statt Konstruktoren

### Naming conventions

- from
- of
- valueOf
- instance/getInstance
- create/newInstance
- getType (zB Files.getFileStore() )
- newType (zb Files.newBufferedReader() )
- type (zB Collections.list() )

### Example

```java
public class MyObject {
  private MyObject(String title, int b, double c) {
      this.title = title;
      this.b = b;
      this.c = c;
  }

  public static MyObject createObjectWithStanardTitle(int a, int b) {
      return new MyObject("Default", a, b);
  }

  public static MyObject createObject(String title, int a, int b) {
      return new MyObject(title, a, b);
  }
}
```

## Builder statt viele Konsturktoren

Wenn ein Konstruktor mehr als 4 Parameter hat, besser einen Builder nutzen.

### Example

```java
public class Book {
    private final String title;
    private final String author;
    private final int pageCount;
    private final String publisher;
    private final String coAuther;

    private Book(Builder builder) {
        this.title = builder.title;
        this.author = builder.author;
        this.pageCount = builder.pageCount;
        this.publisher = builder.publisher;
        this.coAuther = builder.coAuther;
    }

    //Builder als innere Klasse
    public static class Builder {
       //Required
       private final String author;;
       private final String title;

       //Optional
       private int pageCount = 0;
       private String publisher = null;
       private String coAuther = null;

       public Builder(String author, String title) {
            this.author = author;
            this.title = title;
       }

       public Builder pageCount(int pageCount) {
            this.pageCount = pageCount;
            return this;
       }

       public Builder publisher(String publisher) {
            this.publisher = publisher;
            return this;
       }

       public Builder coAuther(String coAuther) {
            this.coAuther = coAuther;
            return this;
       }

       public Book build() {
            return new Book(this);
       }
    }
}
``` 

usage:

```java
Book book = new Book.Builder("Joshua Bloch", "Effective Java")
                       .pageCount(363)
                       .publisher("Pearson")
                       .build();
```

## Singleton Eigenschaft mit privatem Konstruktor erzwingen

### statischer Factory Method:

```java
public class TheOnlyOne {
    private static final TheOnlyOne INSTANCE = new TheOnlyOne();
    private TheOnlyOne() {...}
    public static TheOnlyOne getInstance() { 
        return INSTANCE;
    }
    public void doStuff(){...}
}
```

### ENUM (bevorzugt, falls keine Vererbung)

```java
public enum TheOnlyOne {
    INSTANCE;
    public void doStuff(){...}
}
```

## Reference 

Effective Java by Joshua Bloch
