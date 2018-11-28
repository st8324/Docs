1. 의존성 추가

   * pom.xml에 json 사용을 위한 의존성 추가

   ```xml
   <!-- json 사용 라이브러리 -->
   <dependency>
       <groupId>org.codehaus.jackson</groupId>
       <artifactId>jackson-mapper-asl</artifactId>
       <version>1.9.13</version>
   </dependency>
   <dependency>
       <groupId>com.fasterxml.jackson.core</groupId>
       <artifactId>jackson-databind</artifactId>
       <version>2.8.7</version>
   </dependency>
   ```

   

2. jsp에 ajax 사용

   ```jsp
   $('btn').click(function(){
       $.ajax({
           async:true,
           type:'POST',
           data:id,
           url:"test",
           dataType:"json",
           contentType:"application/json; charset=UTF-8",
           success : function(data){
               console.log(data);
           }
       });
   })
   
   ```

3. controller

   ```java
   @RequestMapping(value ="/test")
   @ResponseBody
   public Map<Object, Object> idcheck(@RequestBody String id){
   
       Map<Object, Object> map = new HashMap<Object, Object>();
       map.put("id", id);
       map.put("pw", "123");
       System.out.println("test");
       return map;
   }
   ```

   