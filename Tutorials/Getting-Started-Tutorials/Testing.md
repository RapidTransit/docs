# Mocking Static Methods

###Overview
Broadleaf contains many static helper methods, trying to run a unit test on a method that contains one of these ie: ```CartState```, ```CustomerState``` etc. requires a different approach to mock easily.

###The Maven Pom
We need to first add a dependency for JMockit which is available on Maven Central (Current version as of this writing is 1.11)
```xml
<dependencies>
    ....
    <dependency>
        <groupId>org.jmockit</groupId>
        <artifactId>jmockit</artifactId>
        <version>1.11</version>
        <scope>test</scope>
    </dependency>
    ...
</dependencies>
```

Then just add it to whichever module's pom will be testing
```xml
<dependencies>
    ....
    <dependency>
        <groupId>org.jmockit</groupId>
        <artifactId>jmockit</artifactId>
    </dependency>
    ...
</dependencies>
```
###Creating the Mock
Unlike Easymock, Mockito and their extensions, the mock can't be autocreated, we need to create a new class for this mock.

Points to consider
* Your Mock must extend ```MockUp<T>```
* The method you are mocking must have the same signature except for the ```static``` modifier.
* You must use JMockit's ```@Mock``` annotation

```java
public class MockCustomerState extends MockUp<CustomerState>{

    Customer customer;

    public MockCustomerState(Customer customer) {
        this.customer = customer;
    }

    @Mock
    public Customer getCustomer(){
        return customer;
    }
}
```

###Using the Mock
Just add the data you want to return and initiate
```java
@Test
public void myTest(){
    ....
    Customer customer = new CustomerImpl();
    customer.setId(1000L);
    new MockCustomerState(customer);
    ...
}
```

###Why it works
A quick explanation why just calling ```new MockCustomerState(customer);``` with out assinging it to anything automaticly binds to ```CustomerState.getCustomer()``` normally mocking frameworks will use a proxy or reflection JMockit is replacing the class loader and it works well with other Mocking frameworks.
