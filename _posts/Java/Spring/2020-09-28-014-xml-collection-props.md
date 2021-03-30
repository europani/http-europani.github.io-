---
layout: post
title: Spring xml 컬렉션 설정(List, Set, Map), Properties
categories: Spring
tags: [Spring]
---

#### List

```java
public class Project {
    private List<String> srcDirs;
    
    public void setSrcDirs(List<String> srcDirs) {
        this.srcDirs = srcDirs;
    } 
}
```

```xml
<bean id="sampleProject" class="ch02.Project">
    <property name="srcDirs">
        <list>
            <value>src</value>			<!-- String -->
            <value>srcResources</value>
        </list>
    </property>
</bean>
```

#### Set

```java
public class Project {
    private Set<String> set1;
    private Set<Integer> set2;
    private Set<DateBean> set3;
    
    public void setSet1(Set<String> set1) {
        this.set1 = set1;
    } 
    
    public void setSet2(Set<Integer> set2) {
        this.set2 = set2;
    } 
    
    public void setSet3(Set<DateBean> set3) {
        this.set3 = set3;
    } 
}
```

```xml
<bean id="sampleProject" class="ch02.Project">
    <property name="set1">
        <set>
            <value>문자열1</value>			<!-- String -->
            <value>문자열2</value>    
        </set>
    </property>
    <property name="set2">
        <set>
            <value type="int">100</value>			<!-- Integer -->
            <value type="int">200</value>
        </set>
    </property>
    <property name="set3">
        <set>
            <ref bean="dataBean">			<!-- DataBean -->
            <ref bean="dataBean">
        </set>
    </property>
</bean>
```

#### Map

```java
public class Project {
    private Map<String, Object> map1;

    public void setMap1(Map<String, Object> map1) {
        this.map1 = map1;
    } 
}
```

```xml
<bean id="sampleProject" class="ch02.Project">
    <property name="map1">
        <map>
            <entry key="k1" value="문자열" />
            <entry key="k2" value="100" value-type="int" />
            <entry key="k3" value-ref="dataBean" />
        </map>
    </property>
</bean>
```

#### Properties

```java
public class Project {
    private Properties additionalInfo;
    
    public void setAdditionalInfo(Properties additionalInfo) {
        this.additionalInfo = additionalInfo;
    } 
}
```

```xml
<bean id="sampleProject" class="ch02.Project">
    <property name="properties">
        <props>
            <prop key="pro1">A</prop>
            <prop key="pro2">B</prop>
        </props>
    </property>
</bean>
```