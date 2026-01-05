# 04. 역할, 책임, 협력
## 협력
- 다수의 연쇄적인 요청과 응답의 흐름으로 구성된다.
- 요청과 응답은 협력에 참여하는 객체가 수행할 책임이다.
```
public InspectionSummaryDto getInspectionSummary(InspectionMainGridSearchCond cond) {
    InspectionSummarySimpleDto s = statisticsRepository.fetchInspectionSummarySimple(cond);
    InspectionSummaryComplexDto c = statisticsRepository.fetchInspectionSummaryComplex(cond);
    InspectionSummaryProgressDto p = statisticsRepository.fetchInspectionSummaryProgress(cond);

    return new InspectionSummaryDto(
            s.totalCount(),
            s.doneCount(),
            s.progressCount(),
            s.canceledCount(),
            s.residentCount(),
            s.todayNewCount(),
            c.unassignedCount(),
            c.delayedCount(),
            c.ncrCount(),
            p.progressRate()
    );
}
```

## 책임
- 책임은 객체의 외부에 제공해 줄 수 있는 정보와 외부에 제공해 줄 수 있는 서비스의 목록이다.
- 책임은 객체의 공용 인터페이스를 구성한다.

## 책임의 분류
- 하는것(doing)  
  : 객체를 생성하거나 계산을 하는 등의 스스로 하는것
  ```
  CodeGroupEntity entity = CodeGroupEntity.of(req);
  CodeGroupEntity saved = repository.save(entity);
  ```
  : 다른 객체의 행동을 시작시키는 것
  ```
  repository.existsByGroupCode(...)
  repository.save(entity)
  repository.findAllByOrderBySortOrderAsc()
  repository.findByGroupCode(...)
  repository.delete(entity)
  ```
  : 다른 객체의 활동을 제어하고 조절하는 것
  ```
  if (!newCode.equals(currentGroupCode) && repository.existsByGroupCode(newCode)) {
      throw new IllegalStateException("이미 존재하는 그룹 코드입니다: " + newCode);
  }
  ```
- 아는것(knowing)  
  : 개인적인 정보에 관해 아는 것
  ```
  public class Account {
      private int balance;
  
      public boolean isOverdrawn() {
          return balance < 0;
      }
  }
  ```
  : 관련된 객체에 관해 아는 것
  ```
  if (!newCode.equals(currentGroupCode) && repository.existsByGroupCode(newCode)) {
      throw new IllegalStateException("이미 존재하는 그룹 코드입니다: " + newCode);
  }
  ```
  : 자신이 유도하거나 계산할 수 있는 것에 관해 아는 것
  ```
  Long sortOrder = request.sortOrder() != null ? request.sortOrder() : entity.getSortOrder();
  ```

## 메시지 전송
- 객체가 다른 객체에게 주어진 책임을 수행하도록 요청을 보내는 것
- 협력을 위해 한 객체가 다른 객체로 접근할 수 있는 유일한 방법

## 역할
- 역할을 이용하면 협력을 추상화함으로써 단순화할 수 있다.
- 역할은 다른 객체에 의해 대체 가능함을 의미한다.

## 객체지향의 선입견
- 시스템의 필요한 데이터를 저장하기 위해 객체가 존재한다.
  - 객체가 존재하는 이유는 행위를 수행하며 협력에 참여하기 위해서
- 객체지향이 클래스와 클래스 간의 관계를 표현하는 시스템의 정적인 측면에 중점을 둔다.
  - 객체가 협력 안에서 어떤 책임과 역할을 수행할 것인지 결정하는 것이 핵심이다.

## 책임-주도 설계
- 객체의 책임과 상호작업에 집중하는 설계
- 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다.
- 시스템 책임을 더 작은 책임으로 분할한다.
- 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다.
- 객체가 책임을 수행하는 중에 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다.
- 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 한다.

## 디자인 패턴
- 유사한 상황에서 반복적으로 적용할 수 있는 책임-주도 설계의 결과물

## 테스트-주도 개발
- 애자일 방법론의 한 종류인 XP의 기본 프랙티스로 소개되면서 주목받기 시작한 설계 기법
- 실패하는 테스트를 작성하고, 테스트를 통과하는 가장 간단한 코드를 작성한 후 리팩터링을 통해 중복을 제거하는 것
- 책임을 수행할 객체 또는 클라이언트가 기대하는 객체의 역할이 메시지를 수신할 때 어떤 결과를 반환하고 그 과정에서 어떤 객체와 협력할 것인지에 대한 기대를 코드의 형태로 작성하는 것


