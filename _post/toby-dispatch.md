## 토비의 봄 1회 - 재사용성과 다이나믹 디스패치, 더블 디스패치


```java
public class Dispatch {
  interface Post {
    void postOn(Sns sns);
  }

  static class Text implements Post {

    @Override
    public void postOn(Sns sns) {
      sns.post(this);
    }
  }

  static class Picture implements Post {

    @Override
    public void postOn(Sns sns) {
      sns.post(this);
    }
  }

  interface Sns {
    void post(Text post);

    void post(Picture post);
  }

  static class FaceBook implements Sns {
    @Override
    public void post(Text post) {
      System.out.println("text - facebook");
    }

    @Override
    public void post(Picture post) {
      System.out.println("picture - facebook");
    }
  }

  static class Twitter implements Sns {
    @Override
    public void post(Text post) {
      System.out.println("text - twitter");
    }

    @Override
    public void post(Picture post) {
      System.out.println("picture - twitter");
    }
  }

  public static void main(String[] args) {
    List<Post> posts = Arrays.asList(new Text(), new Picture());
    List<Sns> snses = Arrays.asList(new FaceBook(), new Twitter());
    posts.forEach(i -> snses.forEach(i::postOn));
  }
}
```
