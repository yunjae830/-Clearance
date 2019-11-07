### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Spring )



# SQL Inject prepare statement #{}, ${} 차이

### '#' 이란?

- #### PreparedStatement를 의미합니다.

  ```mysql
  -- mybatis mapper
  SELECT ID FROM test WHERE ID = #{ID}
  ```

### '$' 란?

- #### Statement를 의미합니다.

  ```mysql
  -- mybatis mapper
  SELECT ID FROM test WHERE num = ${num}
  ```



### '#'과 '$'의 차이

- #### '#'을 사용하게 된다면 오라클에서는 변수를 바인드를 하여 ID값이 달라져도 같은 쿼리로 인식하게 됩니다.

- #### 반면 '$'를 사용한다면 num값이 달라질 경우 다른 쿼리로 인식하기 때문에 새로 파싱 등의 작업으로 속도가 저하될 수가 있습니다.



### 결론

- #### '$'와 같은 경우 자주 바뀌지 않거나 사용자의 입력을 받는 경우가 아닐 때 사용하면 좋습니다.

- #### '#'과 같은 경우는 사용자의 입력을 받는 경우 또는 데이터가 많은 경우 사용합니다.



##  xml`<![CDATA[ ]]>` 사용이유 

### `<![CDATA[ ]]>` 의미

- #### 'Character Data'. 즉, '문자 데이터'를 말하는 것입니다. 이 용어를 더 정확하게 말하면 '(Unparsed) Character Data'. 즉, '파싱하지 않는 문자 데이터' 라는 뜻입니다. 

  #### 반대로, 파싱하는 데이터는 'PCDATA'라고 부릅니다.



### 사용하는 이유

#### 만약 아래의 코드를 실행한다면

```xml
<?xml version="1.0" encoding="UTF-8"?>
<dictionary>
  <term>
    <entry>볼드</entry>
    <description>
        글씨를 굵은 글씨로 강조한다. <b>...</b> 태그를 사용한다. <b> 대신 <strong>을 사용해도 된다.		</description>
  </term>
</dictionary>
```

```result
볼드 
글씨를 굵은 글씨로 강조한다. ... 태그를 사용한다. 대신 을 사용해도 된다.
```

이렇게 출력이 됩니다. 여기서 `<b> </b>`, `<strong>` 이 HTML도 태그로 인식해버려서 제대로 출력되지 않습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<dictionary>
  <term>
    <entry>볼드</entry>
    <description><![CDATA[글씨를 굵은 글씨로 강조한다. <b>...</b> 태그를 사용한다. <b> 대신 <strong>을 사용해도 된다.]]></description>
  </term>
</dictionary>
```

결과는

```result
볼드 
글씨를 굵은 글씨로 강조한다. <b>...</b> 태그를 사용한다. <b> 대신 <strong>을 사용해도 된다.
```

이렇듯 `<![CDATA[ ... ]]>`는 안에 들어가는 텍스트가 파싱되지 않게 하는 기능을 합니다. 이는 파서가 잘못 파싱할 수 있는 텍스트를 다룰 때, 파서의 잘못된 파싱을 방지할 수 있게 하는 것입니다. 예를 들자면 HTML 태그를 텍스트 데이터로 쓴다거나 할 때 등을 들 수 있겠습니다.