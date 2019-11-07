## Lombok

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

    - 해당 생성자 코드에는 UUID 생성 코드가 있어 객체를 생성할 시 반드시 Id 값을 보장받을 수 있습니다.
    - 객체에 대한 생성자를 하나로 두고 그것을 @Builder을 통해서 사용하는 것이 더군다나 효율적이라고 생각합니다.

  - ##### Builder 사용시 매개변수를 최소화하자

    ```java
    @Builder
    public class Member {...}
    ```

    - 클래스 위에 @Builder를 사용 시 @AllArgsConstructor 어노테이션을 붙인 효과를 발생시켜 모든 멤버 필드에 대해서 매개변수를 받는 기본 생성자를 만듭니다.
    - Builder AllArgsConstructor에서 문제가 발생하는데 바로 모든 멤버필드에 대한 매개변수를 허용하게 됩니다. 또 createAt, updateAt 같은 경우는 @CreationTimestamp, @UpdateTimestamp 각각의 어노테이션이 해당 일을 담당하고 있습니다. 이처럼 객체 생성시 받지 않아야 할 데이터 들이 클래스 상단 @Builder를 사용하게 되면 발생하게 됩니다.

  - ```java
    public class Member {
        @Builder
        public Member(String email, String name) {
            this.email = email;
            this.name = name;
        }
    }
    ```

    이렇게 받아야 하는 생성자를 필요조건에 따라 지정하고 그 위에 @Builder를 붙이는게 바람직합니다.

