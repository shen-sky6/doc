```java
@Override  
public void pushToUsers(Set<String> userIds, String message) {  
    // 本地推送  
    for (Channel channel : LOCAL_CHANNELS) {  
        if (isAuthenticated(channel) && userIds.contains(getUserId(channel))) {  
            channel.writeAndFlush(new TextWebSocketFrame(message));  
        }  
    }  
    // Redis 广播  
    PushTask pushTask = new PushTask();  
    pushTask.setMessage(message);  
    pushTask.setUsers(userIds);  
    sendRedisBroadcast(pushTask);  
}  
  
@Override  
public void pushToMatched(Set<String> roleIds, Set<String> postIds, Set<String> deptIds, String companyId, String message) {  
    for (Channel channel : LOCAL_CHANNELS) {  
        if (match(channel, roleIds, postIds, deptIds, companyId)) {  
            channel.writeAndFlush(new TextWebSocketFrame(message));  
        }  
    }  
    PushTask pushTask = new PushTask();  
    pushTask.setMessage(message);  
    pushTask.setPostIds(postIds);  
    pushTask.setRoleIds(roleIds);  
    pushTask.setDeptIds(deptIds);  
    pushTask.setCompanyId(companyId);  
    sendRedisBroadcast(pushTask);  
}
```