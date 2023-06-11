---
title: SpringBoot-WebSocket编程
tags: [SpringBoot,WebSocket]
---

## 后端

### 1、在SpringBoot的pom.xml文件里添加依赖:

```xml
<!-- websocket -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>

```

### 2、在配置中加入 WebSocketConfig.java

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

@Configuration
public class WebSocketConfig {

    /**
     * 注入一个ServerEndpointExporter,该Bean会自动注册使用@ServerEndpoint注解申明的websocket endpoint
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }

}
```

### 3、新建component文件夹，加入WebSocketServer.java

```java
@ServerEndpoint(value = "/chat/{username}")
@Component
public class WebSocketService {


    /**
     * 记录当前在线连接数
     */
    public static final Map<String, Session> sessionMap = new HashMap<>();

    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session, @PathParam("username") String username) {
        Message M = new Message();
        sessionMap.put(username, session);
        M.setUsername(username);
        M.setMessage("----加入到了聊天室");
        String message = JSONObject.fromObject(M).toString();
        System.out.println(username+"已经加入");
        System.out.println(message);
        sendAllMessage(message);
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose(Session session, @PathParam("username") String username) {
        sessionMap.remove(username);
        System.out.println(username+"已经退出");

    }

    /**
     * 收到客户端消息后调用的方法
     * 后台收到客户端发送过来的消息
     * onMessage 是一个消息的中转站
     * 接受 浏览器端 socket.send 发送过来的 json数据
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, Session session, @PathParam("username") String username) {
        Message MM = new Message();
        MM.setUsername(username);
        MM.setMessage(message);
        String sendmsg = JSONObject.fromObject(MM).toString();
        System.out.println("服务器发送数据"+sendmsg);

        sendAllMessage(sendmsg);
    }

    @OnError
    public void onError(Session session, Throwable error) {
        error.printStackTrace();
    }

    /**
     * 服务端发送消息给客户端
     */
    private void sendMessage(String message, Session toSession) {
        try {
            toSession.getBasicRemote().sendText(message);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 服务端发送消息给所有客户端
     */
    private void sendAllMessage(String message) {
        try {
            for (Session session : sessionMap.values()) {
                //System.out.println("服务器发送数据给"+session.getId());
                session.getBasicRemote().sendText(message);
            }
        } catch (Exception e) {
           e.printStackTrace();
        }
    }

}

```

### 4、对接口进行放行

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new WebContentInterceptor())
                .excludePathPatterns("/chat/**");
    }
}
```



## 前端

### Index.vue

```vue
<template>
    <el-input v-model="username" placeholder="请输入名字" />
    <el-button type="primary" v-on:click="conf">确认</el-button>
</template>

<script>
export default {
    name:"inDex",
    data(){
    return{
      username: ""
    }
  },
  methods: {
    conf(){
        if(this.username == ""){
          alert("用户名不能为空")
        }else{
          localStorage.setItem('username',this.username);
          alert("宁当前的用户名是"+localStorage.getItem("username"))
          this.$router.push("/chat")
        }
    }
  }
}
</script>

<style>

</style>
```



### Chat.vue

```vue
<template>
    <el-scrollbar height="400px">
        <el-input v-model="this.data.mysay" placeholder="请输入" />
        <el-button type="primary" v-on:click="send">发送</el-button>
        <p v-for="item in this.data.message" :key="item">{{ item }}</p>
    </el-scrollbar>
</template>

<script>
var sc
export default {
    name:"chAt",
    data(){
        return {
            data:{
                username:"",
                message:[],
                mysay:""
            }
        }
    },
    mounted(){
        let that = this
        if(typeof(WebSocket) == "undefined"){
            alert("您的浏览器不支持WebSocket，无法使用该聊天功能")
        }else{
            if(localStorage.getItem("username") == "undefined" || localStorage.getItem("username") == null){
                alert("您还未输入用户名")
            }else{
                sc = new WebSocket("ws://43.153.105.80:80/chat/"+localStorage.getItem("username"))
                sc.onopen = ()=>{
                    that.data.message.push("您当前已经加入会话")
                }
                sc.onclose = ()=>{
                    that.data.message.push("您已经推出聊天")
                }
                sc.onmessage = (m)=>{
                    let mm = JSON.parse(m.data)
                    //console.log(m.data)
                    that.data.message.push(mm["username"]+"说："+mm["message"])
                }
            }
            
        }
    },
    methods:{
        send(){
            //alert(this.data.mysay)
            sc.send(this.data.mysay)
        }
    }
}
</script>
<style>

</style>
```

ps：vue项目在打包为静态页面时，需要在**vue.config.js**中添加以下代码，否则会出现打包后一片空白的场景

**publicPath: './',**

```javascript
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  publicPath: './',
  transpileDependencies: true
})
```

















