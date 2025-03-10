# Testing Practices Cheat Sheet

## Testing Libraries and Frameworks

- Jest: Primary testing framework
- Mockito: Mocking library for Java
- PowerMock: Extended mocking capabilities for Java

## Mocking and Stubbing Strategies

### Jest Mocking

1. Function mocking:
```javascript
jest.fn()
```

2. Module mocking:
```javascript
jest.mock('module-name')
```

3. Spy on methods:
```javascript
jest.spyOn(object, 'methodName')
```

### Mockito (Java)

1. Mock creation:
```java
@Mock
private DependencyClass mockDependency;
```

2. Stubbing method calls:
```java
when(mockDependency.methodName(anyString())).thenReturn(expectedValue);
```

3. Verify method calls:
```java
verify(mockDependency, times(1)).methodName(anyString());
```

### PowerMock (Java)

1. Mocking static methods:
```java
PowerMockito.mockStatic(StaticClass.class);
when(StaticClass.staticMethod()).thenReturn(expectedValue);
```

2. Mocking private methods:
```java
PowerMockito.spy(objectUnderTest);
PowerMockito.doReturn(expectedValue).when(objectUnderTest, "privateMethodName", any());
```

## Fake Implementations

1. In-memory database for testing:
```java
@Bean
@Profile("test")
public DataSource dataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .addScript("schema.sql")
        .addScript("test-data.sql")
        .build();
}
```

2. Fake HTTP client for API testing:
```javascript
const fakeHttpClient = {
  get: jest.fn(),
  post: jest.fn(),
  // ... other methods
};
```

## Test Structure and Naming Conventions

1. Describe-It pattern for Jest:
```javascript
describe('ComponentName', () => {
  it('should behave in a certain way', () => {
    // Test implementation
  });
});
```

2. Given-When-Then pattern for Java:
```java
@Test
public void givenCondition_whenAction_thenExpectedResult() {
    // Test implementation
}
```

## Assertion Strategies

1. Jest assertions:
```javascript
expect(result).toBe(expectedValue);
expect(array).toContain(item);
expect(object).toHaveProperty('key', value);
```

2. JUnit assertions:
```java
assertEquals(expected, actual);
assertTrue(condition);
assertThat(actual, is(equalTo(expected)));
```

## Test Data Management

1. Factory methods for creating test objects:
```java
private User createTestUser() {
    return new User("testuser", "password", "user@example.com");
}
```

2. Test data builders:
```java
public class UserBuilder {
    private String username = "default";
    private String password = "password";
    private String email = "default@example.com";

    public UserBuilder withUsername(String username) {
        this.username = username;
        return this;
    }

    // ... other builder methods

    public User build() {
        return new User(username, password, email);
    }
}
```

## Test Coverage

- Use Jest's coverage reports for JavaScript/TypeScript
- Use JaCoCo for Java projects

## Integration Testing

1. Spring Boot Test for Java:
```java
@SpringBootTest
@AutoConfigureMockMvc
public class IntegrationTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testEndpoint() throws Exception {
        mockMvc.perform(get("/api/resource"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$.property").value("expectedValue"));
    }
}
```

2. Supertest for Node.js:
```javascript
const request = require('supertest');
const app = require('../app');

describe('API Endpoints', () => {
  it('GET /api/resource should return 200 OK', async () => {
    const response = await request(app).get('/api/resource');
    expect(response.statusCode).toBe(200);
    expect(response.body).toHaveProperty('property', 'expectedValue');
  });
});
```

## Best Practices

1. Use descriptive test names
2. Follow the AAA (Arrange-Act-Assert) pattern
3. Keep tests independent and isolated
4. Use setup and teardown methods for common operations
5. Mock external dependencies and focus on unit behavior
6. Aim for high test coverage, but prioritize critical paths
7. Regularly run and maintain tests to prevent bit rot