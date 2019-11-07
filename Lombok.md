## Lombok 사용방법

### Lombok을 다운받고 이클립스 실행 파일이 있는 곳에 추가합니다.



## @Data는 지양하자

- ##### @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor을 한번에 사용하는 강력한 어노테이션 이지만 그에 따른 부작용도 발생

-  **Setter는 그 의도가 분명하지 않고 객체를 언제든지 변경할 수 있는 상태가 되어서 객체의 안전성이 보장받기 힘듭니다.** 

- ##### 모든 멤버필드에 대해서 Getter를 제공해주는 것은 캡슐화에 좋은 영향을 준다고 생각하지 않습니다.

  - @Getter는 사용하되 최대한 객체가 캡슐화하여 해당 객체가 그 기능을 제공해주는 것이 바람직합니다. 

- ##### ToString으로 인한 양방향 연관관계시 무한 순환 참조

  - ```
    @ToString(exclude = "coupons")
    public class Member {...}
    ```

    클래스 바깥에 위와 같이 선언하는 것이 바람직함

## 바람직한 Lombok 사용방법

#### @NoArgsConstructor 접근 권한을 최소화 하자

-  기본 생성자 접근을 Protected로 제한하자

  - @NoArgsConstructor(access = AccessLevel.PROTECTED) 

  - Protected로 변경하면 외부에서 접근 할 수 없으므로 아래 생성자로 객체 생성합니다

    ```java
    @Builder
    public Product(String name) {
        this.id = UUID.randomUUID().toString();
        this.name = name;
    }
    ```

     **객체에 대한 생성자를 하나로 두고 그것을 @Builder을 통해서 사용하는 것이 더군다나 효율적이라고 생각합니다.** 

#### Builder 사용시 매개변수를 최소화 하자

- ```java
  @Builder
  public class Member {...}
  ```

   클래스 위에 @Builder를 사용 시 @AllArgsConstructor 어노테이션을 붙인 효과를 발생시켜 **모든 멤버 필드에 대해서 매개변수를 받는 기본 생성자를 만듭니다.** 

  