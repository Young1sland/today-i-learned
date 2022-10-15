## Response Class 정의
```java
public class Response<T> {
    private String resultCode;
    private T result;
    
    public static Response<Void> error(String errorCode) { return new Response<>(errorCode, null);}
    public static Response<Void> success(){ return new Response<>("Success", null); }

    //Generic Method
    public static <T> Response<T> success(T result){
        return new Response<>("Success", result);
    }

    public String toStream(){
        if(result == null){
            return "{" +
                    "\"resultCode\":" + "\"" + resultCode + "\"" +
                    "\"result\":" + null + "}";
        }
        return "{" +
                "\"resultCode\":" + "\"" + resultCode + "\"" +
                "\"result\":" +"\"" +  result + "\"" + "}";
    }
}
```

### SnsException 정의
```java
@Getter
@AllArgsConstructor
public class SnsApplicationException extends RuntimeException{
    private ErrorCode errorCode;
    private String message;

    public SnsApplicationException(ErrorCode errorCode){
        this.errorCode = errorCode;
        this.message = null;
    }

    @Override
    public String getMessage() {
        if(message==null){
            return errorCode.getMessage();
        }
        return String.format("%s. %s", errorCode.getMessage(), message);
    }
}
```

## ErrorCode Enum
```java
@Getter
@AllArgsConstructor
public enum ErrorCode {
    DUPLICATED_USER_NAME(HttpStatus.CONFLICT, "User name is duplicated"),
    USER_NOT_FOUND(HttpStatus.NOT_FOUND, "user not found"),
    INVALID_PASSWORD(HttpStatus.UNAUTHORIZED, "Password is invalid"),
    INVALID_TOKEN(HttpStatus.UNAUTHORIZED, "Token is invalid"),
    POST_NOT_FOUND(HttpStatus.NOT_FOUND, "Post not founded"),
    INVALID_PERMISSION(HttpStatus.UNAUTHORIZED, "Permission is invalid"),
    INTERNAL_SERVER_ERROR(HttpStatus.INTERNAL_SERVER_ERROR, "Internal Server Error"),
    ALREADY_LIKED(HttpStatus.CONFLICT, "User already liked the post");

    private HttpStatus status;
    private String message;
}
```




