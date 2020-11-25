# WebAsyncManagerIntegrationFilter

## WebAsyncManagerIntegrationFilter 란?
- Async를 사용할때 웹 MVC를 지원하는 필터.  
- Spring MVC에서 Async 관련 기능을 사용하여 다른 하위 Thread 에서도 같은 SecurityContext를 공유할 수 있도록 해준다.

## 기능 정의 및 예시 
- SpringSecurityContextHolder는 ThreadLocal기반으로 동작, 즉 같은 Thread 내에서만 동작할 수 있다.
- WebAsyncManagerIntegrationFilter는 Spring MVC에서 Async 관련 기능을 사용하여 다른 하위 Thread 에서도 같은 SecurityContext를 공유할 수 있도록 해준다.
- 기능 정의
    - PreProcessing : 다른 Thread에서도 현재 Thread의 SecurityContext를 공유할 수 있도록 하는 전처리 작업.
    - Callable :  비록 다른 쓰레드지만 그 안에서는 동일한 SecurityContext를 참조할 수 있다.
    - PostProcessing : 작업이 종료된 후 SecurityContext를 정리하는 작업. 
   
    ```java
        @GetMapping("/async-handler")
        @ResponseBody
        public Callable<String> asyncHandler() {
            // PreProcessing
            // http-nio-8080-exec 쓰레드
            SecurityLogger.log("MVC");
    
            return new Callable<String>() {
                @Override
                public String call() throws Exception {
                    // task-1 쓰레드
                   SecurityLogger.log("Callable");
                   return "Async Handler";
                }       
            };
            // PostProcessing
        }
    ```
  - http-nio-8080-exec 쓰레드와 task-1 쓰레드의 SecurityContext는 공유 된다.
