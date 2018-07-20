1. 컨트롤러 (HomeController)

```java
@RequestMapping("/member/dup")
@ResponseBody
public Map<Object, Object> idcheck(@RequestBody String id){
    int count = 0;
    Map<Object, Object> map = new HashMap<Object, Object>();
    if(userService.checkUser(id))
        count = 1;
    map.put("cnt", count);
    System.out.println(id);
    return map;
}
```

2.  jsp 파일 중복확인 버튼 클릭 시

```javascript
//id가 dup인 중복 확인 버튼
$("#dup").on("click",function(){
	var id = $("#id").val();//id가 id인 input 태그에 입력된 id 가져오기
	$.ajax({
		async:true,
		type:'POST',
		data:id,
		url:"member/dup",
		dataType:"json",
		contentType:"application/json; charset=UTF-8",
		success : function(data){
			if(data.cnt > 0){
				alert("아이디 존재");
			}else{
				alert("아이디 사용 가능");
			}
		}
	});
});
```

3. pom.xml 의존성 추가

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>
```

