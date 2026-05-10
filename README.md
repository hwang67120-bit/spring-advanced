# Spring Advanced - 과제

## 🎯 과제 개요

Spring Boot 심화 과제: N+1 문제 해결 및 테스트 코드 개선

---

## ✅ 완료 항목

### Lv3. N+1 문제 해결

**문제:**
- `getTodos()` 메서드에서 Todo 조회 시 User를 개별적으로 조회하여 N+1 문제 발생

**해결:**
- `@Query` + `fetch join` → `@EntityGraph`로 변경
- `attributePaths = {"user"}` 설정으로 User와 함께 조회
- 쿼리 1번으로 개선

**변경 사항:**
```java
// Before
@Query("SELECT t FROM Todo t LEFT JOIN FETCH t.user ...")
Page<Todo> findAllByOrderByModifiedAtDesc(Pageable pageable);

// After
@EntityGraph(attributePaths = {"user"})
Page<Todo> findAllByOrderByModifiedAtDesc(Pageable pageable);
```

---

### Lv4. 테스트 코드 수정

#### 1. PasswordEncoderTest
**문제:** `matches()` 메서드 파라미터 순서 오류

**수정:**
```java
// Before
passwordEncoder.matches(encodedPassword, rawPassword);

// After
passwordEncoder.matches(rawPassword, encodedPassword);
```

---

#### 2. CommentServiceTest
**문제:** 예외 타입 불일치

**수정:**
```java
// Before
ServerException exception = assertThrows(ServerException.class, ...);

// After
InvalidRequestException exception = assertThrows(InvalidRequestException.class, ...);
```

---

#### 3. ManagerService
**문제:** `todo.getUser()`가 null일 때 NullPointerException 발생

**수정:**
```java
// Before
if (!ObjectUtils.nullSafeEquals(user.getId(), todo.getUser().getId())) {

// After
if (todo.getUser() == null || !ObjectUtils.nullSafeEquals(user.getId(), todo.getUser().getId())) {
```

---

## 🧪 테스트 결과

모든 테스트 통과 ✅

- PasswordEncoderTest: 1/1 통과
- CommentServiceTest: 1/1 통과
- ManagerServiceTest: 2/2 통과

---

## 📚 학습 내용

### N+1 문제
- Lazy Loading으로 인한 추가 쿼리 발생 문제
- `@EntityGraph`를 통한 효율적인 해결

### 테스트 코드
- 예외 타입과 메시지 정확히 검증
- null 안전성 고려한 로직 작성
- 서비스 로직과 테스트 일치성 확인
