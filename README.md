# Unit testing for C# Developers

### Types of testing
Any type of testing is us writing code that validates code behaviuor.

1. Unit testig
   - Class
   - Method
   - Line of code

2. Integration tesing
   - Call dependencies
   - Scope is bigger

3. E2E testing

### Why it's important?
- Getting feedback of how our system performs is powerful, it can catch bugs before they make it near production.'

### Testing pyramid
![image](https://github.com/p-stojkovski/unit-testing/assets/3589356/2b63d457-e81a-4a0e-835b-2c2f41665332)

### Why unit testing?
- Every time we do a change into the codebase, we need some automated way to verify our system behavior works as expected, and we didn't broke anything.
- Helps you write better code

### Three fundamental testing concepts
1. Testing libary (xUnit, nUnit, MSTest)
2. Mocking libary (Moq, NSubstitute, FakeItEasy)
3. Assertion libary (FluentAssertions, Shouldly)

### Naming
- Should-When approach (ex. Add_ShouldAddTwoNumbers_WhenTwoNumbersAreIntegers)

### XUnit test executing model
- _sut -> system under test
- Creates new istance per execution of test

### Test setup and Teardown
There are two options in xUnit:
- Constructor -> used to do any setup
- IDisposable -> used for cleanup

What if I want async code in my startup or teardown, mostly for integration tests?
- Implement IAsyncLifetime interface methods

Order of execution:
1. Ctor
2. InitialzeAsync (IAsyncLifetime)
3. Test
4. DisposeAsync (IAsyncLifetime)

### Parameterizing tests
- Using Theory and InlineData

### Assertions
- Process of ensuring that what test is expecting and what our code returns is either matching or not.
- FluentAssertions

### Testing private methods
- Private methods are tested by being called by either public or internal method that uses them.
- Do not change private methods to public just to test them, make sure u test the public method that is calling them.

### Testing internal methods
- in new class set: [assembly: InternalsVisibleTo("TESTPROJECTNAME")] -> not recommended way
- Edit .csproj file and add new ItemGroup and add <InternalsVisibleTo Include="TESTPROJECTNAME" /> -> recommended

### What is mocking
- Idea of creating an implmentations of interfaces or virtual methods on abstract classes during runtime to behave as we want.
- NSubstitute to create moq -> Substitute.For<Interface>();
- Moq is an inmemory implementation of an interface that allows us to specify specific behaviour for specific functionality

### The Class Fixture
IClassFixture<T>, very common technique when you want to share any type of dbConnections, docker connections, any type of connection or data that needs to be shared across multiple tests.

### The Collection Fixture
- Shared scoped, context, across mulitple test classes using CollectionFixture.
- Define new class with ICollectionFixture<TClassFixture> with attribute [CollectionDefinition("My collection fixture")] -> use on every test class

### Running test in parallel
- By default XUnit treats each test class as a collection and a test within a collection do not run in parallel.
- Test within a single collection runs one after another.
- Independent collections of eachother will run in parallel.
- [assembly: CollectionBehavior(CollectionBehaviuor.)] or [assembly: CollectionBehavior(DisableTestParallelization = true)] for overriding collection behavior -> not recommended.
- [assembly: CollectionBehavior(DisableTestParallelization = true)] usefull if you have single database instance across any test and you don't want have any clashing, competing tests running in parallel.
- [assembly: CollectionBehavior(MaxParallelThreads = )]
- Can also be overriden in the [CollectionDefinition]

### Advanced parameterization
If need to have a lot of test data used as a Theory and InlineData, you have two options available:
1. Member data 
   - Creating method within the same class of the test that returns the needed data, instead of having inline data, you point to that method.
   - [Theory]
   - [MemberData(nameOf(Method))]
2. Class data
   - New class specificly designed to return dinamicly all the parametars needed for the tests.
   - ClassName : IEnumerable<object[]> -> GetEnumerator() => yield return new object [] { data, data };
   - [Theory]
   - [ClassData(typeOf(Class))]

### Test execution timeouts
- If you have a long running test for any reason that should be done quicly, you can set timeout execution for that test.
- [Fact(Timeout = miliseconds)] -> if running more than specified miliseconds, fail this test.

### Testing Date and Time
- Always inject IDateTimeProvider and mock the date.

### Measuring code coverage
- dotnet tool install -g  coverlet.console
- dotnet build
- coverlet \.bin\Debug\net8.0\Users.Api.Tests.dll --target "dotnet" --targetargs "test --no-build"
- dotnet test --collect:"XPlat Code Coverage" -> generating test results in xml file
- dotnet tool install -g dotnet-reportgenerator-globaltool
- reportgenerator -reports:".\TestResults\<FolderNumber>\coverage.cobertura.xml" -targetdir:"codecoverage" -reporttypes:Html
