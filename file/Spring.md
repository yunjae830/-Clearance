- [뒤로가기](https://yunjae830.github.io/-Clearance/index)



# Spring

### Lombok

Lombok은 자바 컴파일 시점에서 특정 어노테이션으로 해당 코드를 추가할 수 있는 라이브러리입니다. 

- @Data는 @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor을 한번에 사용하는 강력한 어노테이션인 만큼 부작용도 많음

- 올바른 Lombok 사용 예

  ```java
  @Entity
  @Table(name = "member")
  @ToString(exclude = "coupons")
  @Getter
  @NoArgsConstructor(access = AccessLevel.PROTECTED)
  @EqualsAndHashCode(of = {"id", "email"})
  public class Member {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private long id;
      @Column(name = "email", nullable = false)
      private String email;
      @Column(name = "name", nullable = false)
      private String name;
      @CreationTimestamp
      @Column(name = "create_at", nullable = false, updatable = false)
      private LocalDateTime createAt;
      @UpdateTimestamp
      @Column(name = "update_at", nullable = false)
      private LocalDateTime updateAt;
      @OneToMany
      @JoinColumn(name = "coupon_id")
      private List<Coupon> coupons = new ArrayList<>();
      @Builder
      public Member(String email, String name) {
          this.email = email;
          this.name = name;
      }
  }
  ```

  - 무분별한 Setter를 사용하지 않는다. 만약 email의 변경 기능이 제공되지 않는다면 email과 관련된 setter도 제공되지 않도록 합니다.

  - ToString으로 인한 무한 참조 방지. JPA에서 서로 양방향 연관관계인 객체들이 있을 때 ToString을 호출한다면 무한 순한 참조가 발생합니다. 

    ```java
    @ToString(exclude = "상대객체이름")
    public class Member {...}
    ```

    클래스 밖에 위 코드처럼 선언해주면 ToString 항목에서 제외시켜 정상적으로 동작하게 합니다.

  - @Getter는 사용하되 최대한 객체를 캡슐화하여 해당 객체가 그 기능을 제공해주도록 합니다.

  - @NoArgsConstructor 접근 권한을 최소화하여 안전성을 높입니다. 기본 생성자 접근을 protected으로 변경하면 외부에서 해당 생성자를 접근 할 수 없으므로 아래 생성자를 통해서 객체를 생성하게 됩니다.

    ```java
    @Builder
    public Product(String name) {
        this.id = UUID.randomUUID().toString();
        this.name = name;
    }
    ```

