# equals를 재정의하려거든 HashCode도 재정의하라

equals 메서드를 재정의한 클래스 모두에서 hashCode도 재정의해야 한다.
  그렇지 않은 경우에는 hashCode의 일반 규약을 어기게 되므로 해당 클래스의 인스턴스를 HashMap이나 HashSet 같은 컬렉션의 원소로 사용할 때 문제를 일으키게 된다.
  
## hashCode의 일반 규약
- **equals 비교에 사용되는 정보가 변경되지 않았다면,**애플리케이션이 실행되는 동안 객체의`hashCode`메서드는 몇 번을 호출해도**항상 같은 값을 반환**한다. (*단,**애플리케이션을 다시 실행하면 값은 바뀔 수 있다.)*
- **`equals(Object)`가 두 객체를 같다고 판단했으면, 두 객체의 hashCode 값은 항상 같다.**
- **하지만`equals(Object)`가 두 객체를 다르다고 판단했더라도, 두 객채의 hashCode 값은 같을 수 있다.**(해시 충돌)
    - 해시충돌이 일어나게되면 eqauls()를 통해 비교하여 true이면 덮어씌우고 false라면 LinkedList에 추가한다.