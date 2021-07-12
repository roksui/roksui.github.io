---
title: "Lombok 사용법"
---

## Lombok이란?
Lombok은 어노테이션 기반으로 코드를 자동완성 해주는 라이브러리이다.

예를 들어 다음과 같이 건물에 대한 클래스가 있다고 생각해보자.

{% highlight java %}
public class Building {
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;

    public String getBuildingName() {
        return buildingName;
    }

    public void setBuildingName(String buildingName) {
        this.buildingName = buildingName;
    }

    public String getBuildingAddress() {
        return buildingAddress;
    }

    public void setBuildingAddress(String buildingAddress) {
        this.buildingAddress = buildingAddress
    }

    public String getTelephone() {
        return telephone;
    }

    public void setTelephone(String telephone) {
        this.telephone = telephone;
    }

    public double getLatitude() {
        return latitude;
    }

    public void setLatitude(double latitude) {
        this.latitude = latitude;
    }

    public double getLatitude() {
        return latitude;
    }

    public void setLongitude(double longitude) {
        this.longitude = longitude;
    }
}
{% endhighlight %}

위와 같이 getter와 setter 함수만 작성해도 매우 긴 코드가 되고, 추가로 생성자나, toString 함수도 override할 경우 1개의 클래스가 상당히 길어져 가독성이 떨어지는 등의 단점이 생긴다. 만약 위와 같은 코드에 Lombok을 적용하면 아래와 같이 줄일 수 있다.

{% highlight java %}
@Getter
@Setter
public class Building {
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;
}
{% endhighlight %}

위 예시는 모든 변수들에 대해 Getter와 Setter를 적용시킨 예시이다. 훨씬 간결한 코드가 가능해진다.

### Lombok의 여러가지 기능
- @Getter / @Setter
Lombok에서 가장 자주 활용되는 어노테이션으로써 클래스 이름 위에 적용시키면 모든 변수들에 적용이 되고, 변수 이름 위에 적용시키면 해당 변수들만 적용할 수 있다.

{% highlight java %}
@Getter
public class Building {
    
    @Setter
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;
}
{% endhighlight %}

이와 같은 경우, Getter는 모든 변수에 대해 사용가능하지만, Setter는 buildingName에 대해서만 가능해진다.

- @AllArgsConstructor
@AllArgsConstructor는 모든 변수를 사용하는 생성자를 자동완성 시켜준다.

{% highlight java %}
@Getter
@AllArgsConstructor
public class Building {
    
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;

    /* @AllArgsConstructor는 아래의 생성자를 자동 생성해준다.
    public Building(String buildingName, String, buildingAddress, String telephone, double latitude, double longitude) {
        this.buildingName = buildingName;
        this.buildingAddress = buildingAddress;
        this.telephone = telephone;
        this.latitude = latitude;
        this.longitude = longitude;
    }
    */
}
{% endhighlight %}

- @NoArgsConstructor
@NoArgsConstructor는 어떠한 변수도 인자로 갖지 않는 생성자를 자동완성 시켜준다.

{% highlight java %}
@Getter
@NoArgsConstructor
public class Building {
    
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;

    /* @NoArgsConstructor는 아래의 생성자를 자동 생성해준다.
    public Building() {}
    */
}
{% endhighlight %}

- @RequiredArgsConstructor
@RequiredArgsConstructor는 특정 변수만을 활용하는 생성자를 자동완성 시켜준다.
생성자의 인자로 추가할 변수에 @NonNull 어노테이션을 추가해주거나, 해당 변수를 final로 선언하면 된다.

{% highlight java %}
@Getter
@RequiredArgsConstructor
public class Building {

    @NonNull
    private String buildingName;
    private final String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;

    /* @RequiredArgsConstructor는 아래의 생성자를 자동 생성해준다.
    public Building(String buildingName, String buildingAddress) {
        this.buildingName = buildingName;
        this.buildingAddress = buildingAddress;
    }
    */
}
{% endhighlight %}

- @EqualsAndHashCode
@EqualsAndHashCode는 클래스에 대한 equals 함수와 hashCode 함수를 자동으로 생성해준다. 만약 서로 다른 두 객체에서 특정 변수의 이름이 똑같은 경우 같은 객체로 판단을 하고 싶다면 아래와 같이 할 수 있다.

{% highlight java %}
@RequiredArgsConstructor
@EqualsAndHashCode(of = {"buildingName", "buildingAddress"})
public class Building {
    
    @NonNull
    private String buildingName;
    @NonNull
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;

    /* @NoArgsConstructor는 아래의 생성자를 자동 생성해준다.
    public Building() {}
    */
}
{% endhighlight %}

따라서 한 객체의 buildingName과 buildingAddress가 다른 객체의 것과 똑같다면, 같은 객체로 인식하도록 해주는 것이다.

실제로, 다음과 같이 시험해볼 수 있다.
{% highlight java %}
@RestController
@RequestMapping(value = "/building")
@Log4j2
public class BulidingController {

    @GetMapping(value = "/test")
    private ResponseEntity test() {
        Building b1 = new Building("빌딩 이름", "주소1");
        Building b2 = new Building("빌딩 이름", "주소1");
        Building b3 = new Building("빌딩 이름", "주소2");

        log.debug(b1.equals(b2)); // true

        log.debug(b1.equals(b3)); // false

        return ResponseEntity.ok().build();
    }
}
{% endhighlight %}

- @ToString
@ToString은 클래스의 변수들을 기반으로 ToString 메소드를 자동으로 완성시켜 준다. 출력을 원하지 않는 변수에 @ToString.Exclude 어노테이션을 붙여주면 제외시킬 수 있다.

{% highlight java %}
@ToString
@AllArgsConstructor
public class Building {
    
    @ToString.Exclude
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;
}
{% endhighlight %}

위 클래스에 대한 객체를 만들어서 출력해보면,
{% highlight java %}
Building(buildingAddress=랜덤주소, telephone=010000, latitude=32.2342, longitude=235.2342)
{% endhighlight %}

이렇게 결과가 출력된다.

- @Data
@Data를 활용하면 @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor를 자동완성 시켜준다.

- @Builder
@Builder를 사용하면 해당 클래스의 객체의 생성에 Builder패턴을 적용시켜준다. 모든 변수들에 대해 build를 하려면 클래스 위에 @Builder를 붙이면 되고, 특정 변수만을 build하고 싶다면 생성자를 작성하고 그 위에 @Builder를 써주면 된다.

{% highlight java %}
@Getter
@NoArgsConstructor
public class Building {
    
    private String buildingName;
    private String buildingAddress;
    private String telephone;
    private double latitude;
    private double longitude;

    @Builder
    public Building(String buildingName, String buildingAddress) {
        this.buildingName = buildingName;
        this.buildingAddress = buildingAddress;
    }
}
{% endhighlight %}

이 Builder를 아래와 같이 활용할 수 있다.

{% highlight java %}
@RestController
@RequestMapping(value = "/store")
@Log4j2
public class BuildingController {

    @GetMapping(value = "/init")
    private ResponseEntity init() {
        Building building = Building.builder()
            .buildingName("건물이름")
            .buildingAddress("건물주소")
            .build();

        return ResponseEntity.ok(building);
    }
}
{% endhighlight %}



출처: https://mangkyu.tistory.com/78