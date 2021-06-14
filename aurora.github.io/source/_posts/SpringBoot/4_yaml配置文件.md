---
title: 核心功能——配置文件
categories: SpringBoot
---

# YAML配置文件

## 定义

> yaml是一种比`xml`更便捷的文件格式
>
> 主要表现在格式整齐、书写方便、占用内容少

## 语法

采用键值对的形式，key: value

- 一般形式
  - `key: value`
- 数组、队列、集合
  - `key: [v1,v2,v3]`
- Map、对象
  - `key: {k1:v1,k2:v2,k3:v3}`

## 实例

```java
@Data
public class Person {
    
    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;
}

@Data
public class Pet {
    private String name;
    private Double weight;
}
```

```yaml
person:
	userName: whz
	boss: whz
	birth: 1999/10/04
	age: 21
	pet:
		name: cc
		weight: 20
	interests:
		- eat
		- drink
		- play
		- happy
	animal:
		- a
		- b
	score:
		
```

