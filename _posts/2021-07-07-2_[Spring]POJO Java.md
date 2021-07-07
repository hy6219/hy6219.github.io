# POJO Java

Plain Old Java Object

"순수한 자바 오브젝트"

- 이전에는 EJB, Enterprise Java Bean이 인기를 끌었던 영향으로 EJB에 종속적인 부분으로 개발을 진행하였으나, 모듈의 교체나 시스템 업그레이드 시 종속성으로 인해서 불편함이 발생(Enterprise Bean은 비즈니스 로직이 담겨있는 부품!)
- EJB는 미국 Sun Microsystems사가 만든 제품!

🌺 POJO의 특징

1. 특정 규약에 종속되지 않음
2. 특정 환경에 종속되지 않음 - http request, session 등 은 POJO를 위배한 것이고, 어노테이션 기반의 부분은 엄연히 POJO라고 볼 수는 없음!(JPA에 관련된 부분도 해당됨!)

🌺 POJO Framework

1. Spring, Hibernate
- 객체 지향적 설계 및 POJO 지향적인 프레임웍
- 개발자가 서비스 로직에 집중하고, 이를 POJO로 쉽게 개발할 수 있도록 지원

*프레임워크란?

> 소프트웨어의 구체적 부분에 해당되는 설계와 구현을 재사용이 가능하도록 일련의 협업화된 형태로 클래스를 제공하는 것
-랄프 존슨