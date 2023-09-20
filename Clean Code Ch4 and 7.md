# Clean Code Ch4 and 7

## Ch4. annotation

주석에 대한 찬반 의견은 다양하다

- 좋은 함수 이름, 변수 이름 등으로 구성되면, 주석 없는 깔끔한 코드만으로도 코드의 목적을 이해할 수 있다
- 코드 만으로 의도를 표현하지 못할 경우 실패를 만회하기 위해 주석을 쓴다고 필자는 말하고 있다

```c
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다. 
if ((emplotee.flags & HOURLY_FLAG) && (employee.age > 65)

if (employee.isEligibleForFullBenefits())
```

그러나 책에서는 좋은 주석에 대해서도 이야기하고 있다

1. **법적인 주석**
    
    Source code에 들어가며, 저작권 정보나 소유권 정보에 대해 언급한다
    
    ```c
    // Copyright (C) 2003, 2004, 2005 by Object Montor, Inc. All right reserved.
    // GNU General Public License
    ```
    

1. **함수 이름으로 표현될 수 없는 정보에 대해 제공**
    
    ```c
    // 테스트 중인 Responder 인스턴스를 반환
    protected abstract Responder responderInstance();
    // -> responderBeingTested 라는 더 좋은 이름이 존재한다
    
    // kk:mm:ss EEE, MMM dd, yyyy 형식이다.
    Pattern timeMatcher = Pattern.compile("\\d*:\\d*\\d* \\w*, \\w*, \\d*, \\d*");
    ```
    
2. **의도나 경고(위험성)에 대해 설명하는 주석**
    
    어떤 문제를 해결하기 위한 코드에 대한 주석으로 크게 세가지 종류가 있을 수 있는데
    
    - How : 어떻게 문제를 해결했는지를 표현하는 주석
    - What : 무엇을 해결했는지에 대한 주석
    - Why : 왜 이 코드를 작성했는지에 대한 주석
    
    아래로 갈 수록 의도를 설명하는 주석이 되기 때문에, 코드 정보를 효과적으로 전달할 수 있는 좋은 주석이 될 수 있다
    
3. **TODO 주석**
    
    당장 구현하기 어렵지만 언젠가 프로그래머가 해야할 부분을 명시하는 주석
    

## Ch7. Error Handling

<aside>
💡 **Error handling is important, but if it obscures logic, it's wrong.**

</aside>

아래와 같이 실제 로직과 예외 처리 부분을 나누어 구분하여 필요한 부분에만 집중할 수 있도록 한다

```java
public class DeviceController {
  ...
  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }
    
  private void tryToShutDown() throws DeviceShutDownError {
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    pauseDevice(handle); 
    clearDeviceWorkQueue(handle); 
    closeDevice(handle);
  }
  
  private DeviceHandle getHandle(DeviceID id) {
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
  }
  ...
}
```

에러 헨들링의 문법이나 방식에는 크게 4가지가 존재한다

1. **Try-Catch-Finally**
    - try-catch로 프로그램이 어떤 상황에서도 일관적으로 동작하게 하는 stable state로 만들 수 있다
    - 코드의 scope 정의가 가능해진다
    - 아래의 코드에서
        - try, catch 문을 통해 text가 성공적으로 통과될 수 있도록 한다
        - Exception의 범위를 FileNotFoundException으로 줄여서 정확한 Exception을 확인한다
            
            ⇒ 예외가 발생하는 이유와 구체적인 Exception 타입을 정의하는 것이 중요하다
            
    
    ```java
    @Test(expected = StorageException.class)
    
    public List<RecordedGrip> retrieveSection(String sectionName) {
        try {
          FileInputStream stream = new FileInputStream(sectionName);
          stream.close();
        } catch (FileNotFoundException e) {
          throw new StorageException("retrieval error", e);
        }
        return new ArrayList<RecordedGrip>();
      }
    ```
    
    ⇒ 아직 사이드 프로젝트 수준의 에러 헨들링 밖에 안 다뤄봤지만, 이후에 명확한 테스트 코드를 짜야될 때 참고해야 할듯
    

1. **Unchecked Exceptions**
    
    [https://www.nextree.co.kr/p3239/](https://www.nextree.co.kr/p3239/)
    
    개발자가 실수하는 경우에서 발생하는게 대부분이고 디테일한 로직을 신경써서 예외를 구성할 수 없다
    

1. **Exception class**
    
    어떤 방식으로 예외를 잡을 수 있는지, wrapping해서 표현할 수 있는지를 고려해야 한다
    
    ```java
    // Good
      // ACME 클래스를 LocalPort 클래스로 래핑해 new ACMEPort().open() 메소드에서 던질 수 있는 exception들을 간략화
      
      LocalPort port = new LocalPort(12);
      try {
        port.open();
      } catch (PortDeviceFailure e) {
        reportError(e);
        logger.log(e.getMessage(), e);
      } finally {
        ...
      }
      
      public class LocalPort {
        private ACMEPort innerPort;
        public LocalPort(int portNumber) {
          innerPort = new ACMEPort(portNumber);
        }
        
        public void open() {
          try {
            innerPort.open();
          } catch (DeviceResponseException e) {
            throw new PortDeviceFailure(e);
          } catch (ATM1212UnlockedException e) {
            throw new PortDeviceFailure(e);
          } catch (GMXError e) {
            throw new PortDeviceFailure(e);
          }
        }
        ...
      }
    ```
    

Null을 리턴하거나 에러 헨들링에 사용하는 건 좋지 못하다