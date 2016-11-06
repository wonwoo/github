## 토비의 봄 1회 - 재사용성과 다이나믹 디스패치, 더블 디스패치

### 첫 번째 
```java
public class Dispatch {

  interface Post {
    void postOn(SNS s);
  }

  static class Text implements Post {
    @Override
    public void postOn(SNS s) {
      System.out.println("text - " + s.getClass().getSimpleName());
    }
  }

  static class Picture implements Post {
    @Override
    public void postOn(SNS s) {
      System.out.println("picture - " + s.getClass().getSimpleName());
    }
  }

  interface SNS {
  }

  static class Facebook implements SNS {
  }

  static class Twitter implements SNS {
  }

  public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter());
    posts.forEach(p -> sns.forEach(p::postOn));
  }
}
```

### 두 번째 

```java
public class Dispatch1 {
  interface Post {
    void postOn(SNS s);
  }

  static class Text implements Post {
    @Override
    public void postOn(SNS s) {
      if (s instanceof Facebook) {
        System.out.println("text - facebook");
      }
      if(s instanceof Twitter){
        System.out.println("text - twitter");
      }
    }
  }

  static class Picture implements Post {
    @Override
    public void postOn(SNS s) {
      if (s instanceof Facebook) {
        System.out.println("picture - facebook");
      }
      if(s instanceof Twitter){
        System.out.println("picture - twitter");
      }
    }
  }

  interface SNS {
  }

  static class Facebook implements SNS {
  }

  static class Twitter implements SNS {
  }

  public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter());
    posts.forEach(p -> sns.forEach(p::postOn));
  }
}
```

### 세 번째 
```java
public class Dispatch2 {
  interface Post {
    void postOn(SNS s);
  }

  static class Text implements Post {
    @Override
    public void postOn(SNS s) {
      if (s instanceof Facebook) {
        System.out.println("text - facebook");
      }
      if(s instanceof Twitter){
        System.out.println("text - twitter");
      }
      if(s instanceof Linkedin){
        System.out.println("text - linkedin");
      }
    }
  }

  static class Picture implements Post {
    @Override
    public void postOn(SNS s) {
      if (s instanceof Facebook) {
        System.out.println("picture - facebook");
      }
      if(s instanceof Twitter){
        System.out.println("picture - twitter");
      }
    }
  }

  interface SNS {
  }

  static class Facebook implements SNS {
  }

  static class Twitter implements SNS {
  }

  static class Linkedin implements SNS {
  }

  public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter(), new Linkedin());
    posts.forEach(p -> sns.forEach(s -> p.postOn(s)));
  }
}
```

### 네 번째 
```java
public class Dispatch3 {

  interface Post {
    void postOn(Facebook facebook);
    void postOn(Twitter twitter);
  }

  static class Text implements Post {

    @Override
    public void postOn(Twitter twitter) {
      System.out.println("text - facebook");
    }

    @Override
    public void postOn(Facebook facebook) {
      System.out.println("text - twitter");
    }
  }

  static class Picture implements Post {

    @Override
    public void postOn(Twitter twitter) {
      System.out.println("picture - facebook");
    }

    @Override
    public void postOn(Facebook facebook) {
      System.out.println("picture - twitter");
    }
  }

  interface SNS {
  }

  static class Facebook implements SNS {
  }

  static class Twitter implements SNS {
  }

  static class Linkedin implements SNS {
  }

  public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter(), new Linkedin());
    posts.forEach(p -> sns.forEach(s -> p.postOn(s))); //컴파일 에러
  }
}
```
### 마지막 
```java
public class Dispatch4 {

  interface Post {
    void postOn(SNS s);
  }

  static class Text implements Post {
    @Override
    public void postOn(SNS s) {
      s.post(this);
    }
  }

  static class Picture implements Post {
    @Override
    public void postOn(SNS s) {
      s.post(this);
    }
  }

  interface SNS {
    void post(Text text);
    void post(Picture picture);
  }

  static class Facebook implements SNS {
    @Override
    public void post(Text text) {
      System.out.println("text - facebook");
    }

    @Override
    public void post(Picture picture) {
      System.out.println("picture - facebook");
    }
  }

  static class Twitter implements SNS {
    @Override
    public void post(Text text) {
      System.out.println("text - twitter");
    }

    @Override
    public void post(Picture picture) {
      System.out.println("picture - twitter");
    }
  }
  static class Linkedin implements SNS {
    @Override
    public void post(Text text) {
      System.out.println("text - linkedin");
    }

    @Override
    public void post(Picture picture) {
      System.out.println("picture - linkedin");
    }
  }

  public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<SNS> sns = Arrays.asList(new Facebook(), new Twitter(), new Linkedin());
    posts.forEach(p -> sns.forEach(p::postOn));
  }
}
```
